# function

## Spring Security Authenticaiton

- `DelegatingFilterProxy` - `FilterChainProxy` - `SecurityFilterChain` - `SecurityFilter`

- `FilterChainProxy`是Spring Security的切入点/起始点，出问题时可在此类中打点。

- give more flexibility in determining when a `SecurityFilterChain` should be invoked

- `Authentication`: 代表一个已被验证的用户User, 将会置入`AuthenticationManager`用于为已验证的用户提供证书。 common implement: `UsernamePasswordAuthenticationToken`
  - contains `principle`-鉴别用户，通常为`UserDetails`实例; 
  - `credentials`-通常为password，在确定验证后将会清除以防泄露; 
  - `authorizes`-权限，由`GrantedAuthority`代表，通常为"ROLE_XXX"，通常从`AuthenticationProvider`提供的`UserDetails`得来的
  - 如`UsernamePasswordAuthenticationToken`中，field `credentials` 为用户请求时输入的密码， 而 field `principle` 为通过内置存储（缓存/持久化存储）得到的已注册用户，通过两者的比较确定`credentails`是否正确

- `GrantedAuthority`在`UserDetailsService`生成`User`时注入进去。如`JdbcDaoImpl`通过`roles(String ...)`对`User`的 authorities 进行设定，使用`SimpleGrantedAuthority`封装。

- `AuthenticationManager` is the API that defines how Spring Security’s Filters perform authentication.
  - While the implementation of `AuthenticationManager` could be anything, the most common implementation is `ProviderManager`.（常用的`AuthenticationManager`实现类为`ProviderManager`）
  - `AuthenticationProvider`接收由`Filter`生成的`Authentication`并根据已有信息进行验证，若验证成功将会基于原本的`Authentication`生成新的`Autentication`。

- `AbstractAuthenticationProcessingFilter`: 一个用于验证用户证书(credential)的基本Filter类。其中一子类为`UsernamePasswordAuthenticationFilter`。此类中通过`setter`包含了`AuthenticationManager`（`ProviderManager`为实现类）

- `AuthenticationEntryPoint`用于已有的request信息和userDetails信息封装进`ResponseEntity`并向客户端发送需要验证的响应。通过`commence(HttpServletRequest, HttpServletResponse, AuthenticationException)`

- `GrantedAuthority`, `AccessDecisionManager`, `AccessDecisionVoter` relation:

  - `GrantedAuthority`: 由`AuthenticationManager`将其注入进`Authentication`中并随后由`AccessDecisionManager`从`Authenticaiton`中读取其信息，通过接口`String getAuthority()`获得。

  - `AccessDecisionManager`: 在构造此具体化实例时需要注入`Collection<AccessDecisionVoter<?>>`, 可理解为`AccessDecisionVoter`的封装并管理。在实际使用`decide(Authentication authentication, Object secureObject, Collection<ConfigAttribute> attrs)`时将引用到`AccessDecisionVoter`。`Object secureObject`, 将对象中所拥有的参数注入其中并用于检查?。

    - 三个实现（策略）：`ConsensusBased`, `AffirmativeBased`, `UnanimousBased`。
      - `ConsensusBased`为公平投票机制，少数服从多数。
      - `AffirmativeBased`为一票决定(+)制，一票赞成则授权
      - `UnanimousBased`为一票决定(-)制，一票否定则拒绝

  - `AccessDecisionVoter`: 投票器，根据导入的`Authentication`进行投票，通过`vote():int`返回结果，`ACCESS_GRANTED = 1`表示授权，`ACCESS_ABSTAIN = 0`表示对验证没处理手段，`ACCESS_DENIED = -1`表示拒绝。

  - `Authentication#getAuthority():String`, `AccessDecisionVote#support(ConfigAttirbute)`, `AccessDecisionManager#support(ConfigAttribute)`三者方法均围绕`String`进行逻辑处理 

- Java Configutation of Authentication and Authorization:
  ```java
  protected void configure(HttpSecurity http) throws Exception {
    http
        // ...
        // By default, Spring Security’s authorization will require all requests to be authenticated. The explicit configuration looks like:
        .authorizeRequests(authorize -> authorize
            .anyRequest().authenticated()
        );
  }
  ```  
  ```java
  protected void configure(HttpSecurity http) throws Exception {
    http
        // ...
        .authorizeRequests(authorize -> authorize                                  
            .mvcMatchers("/resources/**", "/signup", "/about").permitAll()         
            .mvcMatchers("/admin/**").hasRole("ADMIN")                             
            .mvcMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')") 
            // 对于没match的url，对于任意的请求均拒绝  
            .anyRequest().denyAll()                                                
        );
  }
  ```
- OAuth2 Authentication request 中的"state"参数在spring security中与其中一个`OAuth2AuthorizationRequest`实现相对应。(`HttpSessionOAuth2AuthorizationRequestRepository` line:79 `OAuth2AuthorizationRequest originalRequest = authorizationRequests.remove(stateParameter);`)
- `OAuth2LoginAuthenticationManager`中的"access_token"最终来自于`ClientRegistration`-从request请求的封装类`HttpServletRequest`中的`getattribute(OAuth2ParameterNames.REGISTRATION_ID)`中获得(`OAuth2LoginAuthenticationFilter` line:166)。
- 使用`HttpSecurity#addFilter(Filter)`时, 加入的Filter需要实现/继承Spring Framework中提供的Filter接口/Filter抽象或实现类。如`AbstractAuthenticationProcessingFilter`

# 3.
- jwk, jws:
  - jwk传输公钥，私钥存储于AuthorizaitonServer中，私钥用于加密jwk生成signature并融合进jwk中(jws)。ResourceServer使用jwk中的公钥对jws进行验证
- openid，jwt中必须参数的重合成员

## CommonOAuth2Provider config
first way, yaml
```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          okta:
            client-id: okta-client-id
            client-secret: okta-client-secret
        provider:
          okta: 
            authorization-uri: https://your-subdomain.oktapreview.com/oauth2/v1/authorize
            token-uri: https://your-subdomain.oktapreview.com/oauth2/v1/token
            user-info-uri: https://your-subdomain.oktapreview.com/oauth2/v1/userinfo
            user-name-attribute: sub
            jwk-set-uri: https://your-subdomain.oktapreview.com/oauth2/v1/keys
```
second way, Override the Auto-configuration with `ClientRegistrationRepository`:
```java
@Configuration
public class OAuth2LoginConfig {

    @EnableWebSecurity
    public static class OAuth2LoginSecurityConfig extends WebSecurityConfigurerAdapter {

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests(authorize -> authorize
                    .anyRequest().authenticated()
                )
                .oauth2Login(withDefaults());
        }
    }

    @Bean
    public ClientRegistrationRepository clientRegistrationRepository() {
        return new InMemoryClientRegistrationRepository(this.googleClientRegistration());
    }

    // config here
    private ClientRegistration googleClientRegistration() {
        return ClientRegistration.withRegistrationId("google")
            .clientId("google-client-id")
            .clientSecret("google-client-secret")
            .clientAuthenticationMethod(ClientAuthenticationMethod.BASIC)
            .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
            .redirectUriTemplate("{baseUrl}/login/oauth2/code/{registrationId}")
            .scope("openid", "profile", "email", "address", "phone")
            .authorizationUri("https://accounts.google.com/o/oauth2/v2/auth")
            .tokenUri("https://www.googleapis.com/oauth2/v4/token")
            .userInfoUri("https://www.googleapis.com/oauth2/v3/userinfo")
            .userNameAttributeName(IdTokenClaimNames.SUB)
            .jwkSetUri("https://www.googleapis.com/oauth2/v3/certs")
            .clientName("Google")
            .build();
    }
}
```

### Tips of ClientRegistration
- 一个client中一般不仅仅只向一个授权服务器请求验证和授权，同时会包含多个，此时多个`ClientRegistration`代表分别对多个授权服务器搭建验证联系的具体信息存储类。
### Tips of OAuth2UserRequest
- 每个`OAuth2UserRequest`中都分别带有`ClientRegistration`和`OAuth2AccessToken`，`ClientRegistration`与`OAuth2UserRequest`，`OAuth2UserRequest`之间的对应关系（这个用户和accesstoken从哪里来
）

## Claims （Represent Jwt content）
- `ClaimAccessor`的继承类中均有`getClaims()`, 如`Jwt`, `OidcIdToken`等，返回`Map<String, Object>`，表示返回体jwt中拥有的内容。
- 虽然返回的的jwt均为string，可理解为`Map<String, String>`，但为了方便使用在应用中传输的Claims中的value会从`String`不断封装成具体实例`Object`以供使用。

## HttpSecurity#oauth2Login()
```java
@EnableWebSecurity
public class OAuth2LoginSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .oauth2Login(oauth2 -> oauth2
                .clientRegistrationRepository(this.clientRegistrationRepository())
                .authorizedClientRepository(this.authorizedClientRepository())
                .authorizedClientService(this.authorizedClientService())
                // 明确要登录的端点
                .loginPage("/login")
                .authorizationEndpoint(authorization -> authorization
                    // 提供一个接口，用户访问此接口时将使用对应的ClientRegistration进行授权服务器的授权认证的访问.
                    .baseUri(this.authorizationRequestBaseUri())
                    // Sets the repository used for storing OAuth2AuthorizationRequest's.
                    .authorizationRequestRepository(this.authorizationRequestRepository())
                    // Sets the resolver used for resolving OAuth2AuthorizationRequest's.
                    .authorizationRequestResolver(this.authorizationRequestResolver())
                )
                .redirectionEndpoint(redirection -> redirection
                    // 设定uri后需确定ClientRegistration.redirectUriTemplate()中的值与之相对应
                    // 如baseUri: /login/oauth2/callback/*, redirectUriTemplate: {baseUrl}/login/oauth2/callback/{registrationId}
                    .baseUri(this.authorizationResponseBaseUri())
                )
                .tokenEndpoint(token -> token
                    //Sets the client used for requesting the access token credential from the Token Endpoint.
                    //  OAuth2AccessTokenResponseClient<OAuth2AuthorizationCodeGrantRequest>
                    .accessTokenResponseClient(this.accessTokenResponseClient())
                )
                .userInfoEndpoint(userInfo -> userInfo
                    // Mapper 将OAuth2User.getAuthorities()/OidcUser.getAuthorities()获得的权限封装至Set中并用于OAuth2AuthenticationToken以帮助完成验证
                    // 最终Mapper将传递给OAuth2User or OAuth2UserRequest/OidcUser or OidcUserRequest 
                    // (Request: 代表一个将要传送到user info endpoint 的请求，将会在OAuth2UserSevice中被初始化)
                    .userAuthoritiesMapper(this.userAuthoritiesMapper())
                    // 自定义策略的userservice，request中带有从授权服务器得来的access token(OAuth2AccessToken封装)
                    // 此处的userservice将对request在访问userinfo endpoint之前进行初始化设置
                    // OAuth2UserService(Interface) 拥有从userinfo endpoint 获得resource owner的user attribute并将attribute提供至client（ClientRegistration）并生成相对应resource owner的user所拥有的权限(AuthenticatedPrincipal)
                    // client并不需要spring User去定义user，而是根据user info返回的信息去定义。
                    .userService(this.oauth2UserService())
                    .oidcUserService(this.oidcUserService())
                    .customUserType(GitHubOAuth2User.class, "github")
                )
            );
    }
}
```
## userinfo EndPoint 返回的信息
返回的信息需由验证服务器提供商声明具体提供了哪些信息