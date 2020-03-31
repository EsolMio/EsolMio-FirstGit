# Tips of OAuth2
## 凡是对token endpoint进行请求的均需要验证客户端。


Link : [User Authentication with OAuth 2.0](https://oauth.net/articles/authentication/)

## OAuth在搭建验证/授权平台的作用
- 就像巧克力(chocolate)和软糖(fudge)，前者为成分，后者为成品，巧克力可用于制造软糖(成分)，软糖中可拥有巧克力，但不能说软糖中必须有巧克力，巧克力必须用于制造软糖。OAuth与验证/授权平台也是如此，没有OAuth也可搭建平台。

## OAuth token作用对象
- token作用的对象为受保护的资源(proteceted resource)，对用户/客户端是透明的(opaque)。受保护资源不会通过token判断用户是否存在/有效，根据OAuth协议的定义，用户无权在客户端(client)和保护资源(proteced resource)之间做任何动作。所以需要一个针对客户端的工件(artifact)，如双重目的(dual-purposing)访问(access)token，并制定一种可被客户端识别并理解的格式。OpenID Connect\`s token 和 Facebook Connect\`s作为辅助(secondray)token，将验证信息发送至客户端。

## 接收其他来源的token（token注入）
- 当客户端接收其他资源返回的token时，这种行为是危险的。在客户端将token设为url哈希参数而没有使用OAuth state 参数时将会发生上述情况。或者应用程序在不同部件之间（多个服务器）进行访问的同时携带了token也会发生。

## 验证过程（根据 Spring Cloud in Action p166 pic 7-1 *推测* ）
```
1. user -(post:username, password)-> application server
2. application server -(post: user attribute; for: authentication and authorization)-> OAuth2 server(~/auth/oauth/token)
3. OAuth2 server -(post: user token)-> application server(save user token like sessionMap [ sessionId : user_token ] )
4. application server -(post: user token)-> protected resource server
5. protected resource server -(post: user token; for:check token)-> OAuth2 server(~/auth/user; Tips:this URL definied by dev)
```

## OAuth2 抽象
URL : [RFC 6750](https://tools.ietf.org/html/rfc6750)
```
     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+

                     Figure 1: Abstract Protocol Flow
```

# JWT

## 多种形态
- 已签名JWT->JWS(JSON Web Signature)
- 加密JWT->JWE(JSON Web Encode ? )
- JWT并不会单独出现/存在，而是以JWS/JWE的形式出现，类似JWT为抽象类，JWS/JWE为实现类

## 验证
- JWT自带公钥进行数据完整性验证，但不够
- 需要验证JWT是否可信
- 需要在每个服务端配置受信证书，但此举不适合微服务的特性和部署（不易于管理证书）
- 可建立统一的证书中心(CA-Certificate Authority)，同时为多个组件配置中介证书中心
- 此举将降低将证书分布各个组件所带来的配置管理负担
- TLS(Transport Layer Security)-传输层安全性协议

## 单点登录
- 一次登录即可根据授予的权限访问范围内的微服务

## JWT携带信息
- 包含用户的部分属性，如：first_name, last_name, email等等，微服务可通过JWT检索对应的用户属性获得对应的用户信息

## 客户端授权码模式
### Client Authentication - 客户端验证; Client Identifier - 客户端身份
- 客户端验证可在获得授权码，访问码，刷新授权码、访问码时使用
- 在进行客户端验证时，需要client在初次访问Authorization Server(AS)时将代表本client的Client Identifier(CI, 客户端身份)传递至AS，AS将会记录CI并对所有的Resource Owner暴露。
- CI表示参数: *client_id*
- 若client需要通过 Http Basic authentication scheme ( [RFC2617](https://tools.ietf.org/html/rfc2617) ) 同AS验证，则需要配上参数: *client_secret*
- 两参数建议在request-body中使用，而不是直接暴漏在URI中，且授权服务器与客户端的连接需建立在TLS上。
  ```
  Example: refresh Access Token

   POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA&scope=xxxx
  ```
### [Authorization Code Grant](https://tools.ietf.org/html/rfc6749#section-4.1)
```
     +----------+
     | Resource |
     |   Owner  |
     |          |
     +----------+
          ^
          |
         (B)             (Authorization request)
     +----|-----+          Client Identifier      +---------------+
     |         -+----(A)-- & Redirection URI ---->|               |
     |  User-   |                                 | Authorization |
     |  Agent  -+----(B)-- User authenticates --->|     Server    |
     |          |                                 |               |
     |         -+----(C)-- Authorization Code ---<|               |
     +-|----|---+                                 +---------------+
       |    |                                         ^      v
      (A)  (C)                                        |      |
       |    |                                         |      |
       ^    v                                         |      |
     +---------+                                      |      |
     |         |>---(D)-- Authorization Code ---------'      |
     |  Client |          & Redirection URI                  |
     |         |                                             |
     |         |<---(E)----- Access Token -------------------'
     +---------+       (w/ Optional Refresh Token)

   Note: The lines illustrating steps (A), (B), and (C) are broken into
   two parts as they pass through the user-agent.
```
- 流程：
  - A：客户端在需要获得用户批准授权时，需将用户重定向到authorization endpoint, 重定向中将会包括传入endpoint的所有必须参数。
  - B：若用户没有在授权服务器中登录，则要求登录并判断是否授权
  - C：获得授权后授权服务器将用户重定向至client，注意，此时授权服务器中的respoonse内容将会通过重定向的方式传送给user，其中带有参数"token", "state"等等。**重定向意味着响应的信息将由client通过请求的方式发送给client**
- Tips: 详细看URL。提示：(A)中Redirection URI，此处的重定向地址指向为客户端Client，用于在(B)用户确定授权后，在(C)处将User-Agent（用户代理-指Resource Owner）重定向回Client。(D)处的Redirection URI与(A)中相同时，授权服务器才将token下发Client
### Authorization request claim - 授权请求时的要求：
- RESTful: GET
- format: application/x-www-form-urlencoded (如果参数均置于URL中则无需声明conten-type: application/x-www-form-urlencoded)
- parameters: 1. response-type(**required**, Value **MUST** set to `code`) ; 2. client_id(**required**, client indentifier.  A client MAY use the "client_id" request parameter to identify itself when sending requests to the token endpoint ) ; 3. redirect_uri(optional) ; 4. scope(optional) ; 5. state(recommened - 非透明值，用于客户端保持请求和接收回应时的转态，授权服务器会在获得用户授权并将用户(由user-agent代表)重定向至redirect_uri时附带此参数值，用于防范CSRF攻击)
  > Example: GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
    Host: server.example.com
### Authorization response-授权服务器对发送至Authorization endpoint的请求（授权请求）的响应
- 响应将发送到request中设定的redirect_uri
- parameters: 1. code(REQUIRED, 授权码，有效时间必须短暂以减小授权码被泄露的风险(小到什么时候才合理)，若one code被使用大于一次必须拒绝相应的请求并撤销 **全部的** token) ; 2. state(REQUORED, ) 
### Authorization Error Response
...
### Access Token Request claim - 访问权限token请求
- RESTful: GET
- format:application/x-www-form-urlencoded
- parameters: 
  1. grant_type(REQUIRED.  Value **MUST** be set to `authorization_code`.)
  2. code(REQUIRED, the Authorization Code received from Authorizaiton Server)
  3. redirect_uri(REQUIRED, the value which same as Authorizaiton Request redirect_uri parameter)
- Example:
  ```
  POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
     &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
  ```

## Implicit Grant - 隐式准许
流程图：
```
     +----------+
     | Resource |
     |  Owner   |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier     +---------------+
     |         -+----(A)-- & Redirection URI --->|               |
     |  User-   |                                | Authorization |
     |  Agent  -|----(B)-- User authenticates -->|     Server    |
     |          |                                |               |
     |          |<---(C)--- Redirection URI ----<|               |
     |          |          with Access Token     +---------------+
     |          |            in Fragment
     |          |                                +---------------+
     |          |----(D)--- Redirection URI ---->|   Web-Hosted  |
     |          |          without Fragment      |     Client    |
     |          |                                |    Resource   |
     |     (F)  |<---(E)------- Script ---------<|               |
     |          |                                +---------------+
     +-|--------+
       |    |
      (A)  (G) Access Token
       |    |
       ^    v
     +---------+
     |         |
     |  Client |
     |         |
     +---------+

 Note: The lines illustrating steps (A) and (B) are broken into two parts as they pass through the user-agent.

```

## EndPoint 端点
[OAuth 2.0](https://tools.ietf.org/html/rfc6749#page-18)
- 协议端点
    - Authorization endpoint - 授权端点：客户端申请获得授权码(Authentication Code)的端点，需走[TLS](https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E5%B1%A4%E5%AE%89%E5%85%A8%E6%80%A7%E5%8D%94%E5%AE%9A) ( need SSR )
      > used by the client to obtain authorization from the resource owner via user-agent redirection
    - Token endpoint - 令牌端点：客户端通过授权码获得同行令牌的端点
      > used by the client to exchange an authorization grant for an access token, typically with client authentication.
    - Redirection endpoint - 重定向端点：授权服务器将授权证书响应返回至客户端的端点
      > used by the authorization server to return
      responses containing authorization credentials to the client via
      the resource owner user-agent
      
      重定向端点存在的意义：防止被黑客通过使用Open Redirector仿造钓鱼网站而获得用户Authentication Code，并通过Authentication Code向其他Protected Resource进行资源上的访问与使用。可理解为一个Redirect URL 对应一个Authentication Code，如果不对应在Token endpoint上会被拒绝。

## OpenID Connection 与 OAuth2 的结合
[OpenID Connection](https://openid.net/specs/openid-connect-core-1_0.html)

大致流程图:
```
+--------+                                   +--------+
|        |                                   |        |
|        |---------(1) AuthN Request-------->|        |
|        |                                   |        |
|        |  +--------+                       |        |
|        |  |        |                       |        |
|        |  |  End-  |<--(2) AuthN & AuthZ-->|        |
|        |  |  User  |                       |        |
|   RP   |  |        |                       |   OP   |
|        |  +--------+                       |        |
|        |                                   |        |
|        |<--------(3) AuthN Response--------|        |
|        |                                   |        |
|        |---------(4) UserInfo Request----->|        |
|        |                                   |        |
|        |<--------(5) UserInfo Response-----|        |
|        |                                   |        |
+--------+                                   +--------+

```

## Spring Security `ClientRegistrationRepository`
- 在Spring `ClientRegistrationRepository`中定义了customer provider properties需要设定的值：
- 以下例子定义了如何Override Spring Boot Auto-configuration，在不Override的情况下Auto-configuration将会通过`application.yml`定义的`spring.security.oauth2.client.registration.[registrationId]...`进行配置。
  ```java
  @Configuration
  public class OAuth2LoginConfig {

   @Bean
    public ClientRegistrationRepository clientRegistrationRepository() {
        return new InMemoryClientRegistrationRepository(this.googleClientRegistration());
    }

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
  same as
  ```yml
  spring:
  security:
    oauth2:
      client:
        registration:
          okta:
            client-id: okta-client-id
            client-secret: okta-client-secret
        provider:
          okta: 1
            # 根据OAuth2协议要求，必须提供 three protocol endpoint
            # 分别为: Authorizaiton Endpoint(use by client), Token Endpoint(use by client), Redirect Endpoint(use by client)
            authorization-uri: https://your-subdomain.oktapreview.com/oauth2/v1/authorize
            token-uri: https://your-subdomain.oktapreview.com/oauth2/v1/token

            # OpenID Connetion 要求，提供UserInfo Endpoint
            # "To obtain the requested claims about the end-user, the client makes a request to the UserInfo Endpoint by using an access token obtained through OpenID Connect Authentication"
            user-info-uri: https://your-subdomain.oktapreview.com/oauth2/v1/userinfo
            user-name-attribute: sub
            jwk-set-uri: https://your-subdomain.oktapreview.com/oauth2/v1/keys
  ```

  
