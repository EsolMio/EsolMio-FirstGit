## clientId, paremeter "state" in authorizaiotn request and response
clientId(registrationId)用于确定所使用到的授权服务器，state将代表本用户(user)。

## `InMemoryClientRegistrationRepository`中将`List`置换为`ConcurrentHashMap`
- Example Code:
  ```java
  private static Map<String, ClientRegistration> toUnmodifiableConcurrentMap(List<ClientRegistration> registrations) {
		ConcurrentHashMap<String, ClientRegistration> result = new ConcurrentHashMap<>();
		for (ClientRegistration registration : registrations) {
			if (result.containsKey(registration.getRegistrationId())) {
				throw new IllegalStateException(String.format("Duplicate key %s",
						registration.getRegistrationId()));
			}
			result.put(registration.getRegistrationId(), registration);
		}
        // UnmodifiableMap的实现Tips: 实现Map Interface的同时将可修改的方法实现为throw new UnsupportedOperationException()
		return Collections.unmodifiableMap(result);
	}
  ```

  ## Spring Security Reference-Title: OAuth2 Login
  - 此章节所讲解的OAuth2 Login适用于Client Server，非Authorization Server

## `OAuth2UserRequestEntityConverter`: How to convert an `OAuth2UserRequest` to a `RequestEntity`
```java
public class OAuth2UserRequestEntityConverter implements Converter<OAuth2UserRequest, RequestEntity<?>> {
	private static final MediaType DEFAULT_CONTENT_TYPE = MediaType.valueOf(APPLICATION_FORM_URLENCODED_VALUE + ";charset=UTF-8");

	@Override
	// convert中先初步设置了HttpRequest的方法，头部设定为"Accept: application/json, Authotization: Bearer access_token"
	public RequestEntity<?> convert(OAuth2UserRequest userRequest) {
		// 使用clientRegistration
		ClientRegistration clientRegistration = userRequest.getClientRegistration();

		//默认使用GET方法，默认的（ClientRegistration中无设定）AuthenticationMethod为HEADER
		HttpMethod httpMethod = HttpMethod.GET;
		// 如果验证方法为FORM，则将HTTPMethod切换为Post
		if (AuthenticationMethod.FORM.equals(clientRegistration.getProviderDetails().getUserInfoEndpoint().getAuthenticationMethod())) {
			httpMethod = HttpMethod.POST;
		}
		// 使用Spring提供的Http Header 工具封装
		HttpHeaders headers = new HttpHeaders();
		// 设置http头部的Accept参数为"application/json"-"Accept: application/json"
		headers.setAccept(Collections.singletonList(MediaType.APPLICATION_JSON));
		// 获得UserInfo的uri
		URI uri = UriComponentsBuilder.fromUriString(clientRegistration.getProviderDetails().getUserInfoEndpoint().getUri())
				.build()
				.toUri();

		RequestEntity<?> request;
		if (HttpMethod.POST.equals(httpMethod)) {
			headers.setContentType(DEFAULT_CONTENT_TYPE);
			MultiValueMap<String, String> formParameters = new LinkedMultiValueMap<>();
			formParameters.add(OAuth2ParameterNames.ACCESS_TOKEN, userRequest.getAccessToken().getTokenValue());
			request = new RequestEntity<>(formParameters, headers, httpMethod, uri);
		} else {
			// 设置http头部参数"Authotization: Bearer access_token"
			headers.setBearerAuth(userRequest.getAccessToken().getTokenValue());
			// 使用三个参数：HttpHeader, HttpMethod, URI生成RequestEntity
			request = new RequestEntity<>(headers, httpMethod, uri);
		}

		return request;
	}
}
```

## `DefaultOAuth2UserService` 对`loadUser(OAuthUserRequest request)`的实现
```java
@Override
public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
	Assert.notNull(userRequest, "userRequest cannot be null");

    // 检查ClientRegistration中是否有设定uri
	if (!StringUtils.hasText(userRequest.getClientRegistration().getProviderDetails().getUserInfoEndpoint().getUri())) {
		OAuth2Error oauth2Error = new OAuth2Error(
			MISSING_USER_INFO_URI_ERROR_CODE,
			"Missing required UserInfo Uri in UserInfoEndpoint for Client Registration: " +
				userRequest.getClientRegistration().getRegistrationId(),
			null
		);
		throw new OAuth2AuthenticationException(oauth2Error, oauth2Error.toString());
	}
	// 因为使用OpenID Connect，userNameAttributeName设定为IdTokenClaimNames.SUB，即ID Token中的"sub"
	String userNameAttributeName = userRequest.getClientRegistration().getProviderDetails()
			.getUserInfoEndpoint().getUserNameAttributeName();
	if (!StringUtils.hasText(userNameAttributeName)) {
		OAuth2Error oauth2Error = new OAuth2Error(
			MISSING_USER_NAME_ATTRIBUTE_ERROR_CODE,
			"Missing required \"user name\" attribute name in UserInfoEndpoint for Client Registration: " +
				userRequest.getClientRegistration().getRegistrationId(),
			null
		);
		throw new OAuth2AuthenticationException(oauth2Error, oauth2Error.toString());
	}
	// 如上，获得RequestEntity
	RequestEntity<?> request = this.requestEntityConverter.convert(userRequest);

	ResponseEntity<Map<String, Object>> response;
	try {
		// 使用RestTemplate(实现了RestOperations)通过RequestEntity获得ResponseEntity，获得ResponseEntity即意味着RestTemplate已完成了RequestEntity的转发
		// PARAMETERIZED_RESPONSE_TYPE = new ParameterizedTypeReference<Map<String, Object>>()，
		// 通过泛型参数(Parameterized Type, generic type)的设置设定返回类型，如此处为Map<String, Object>, 用于runtime时的判定
		response = this.restOperations.exchange(request, PARAMETERIZED_RESPONSE_TYPE);
	} catch (OAuth2AuthorizationException ex) {
		OAuth2Error oauth2Error = ex.getError();
		StringBuilder errorDetails = new StringBuilder();
		errorDetails.append("Error details: [");
		errorDetails.append("UserInfo Uri: ").append(
				userRequest.getClientRegistration().getProviderDetails().getUserInfoEndpoint().getUri());
		errorDetails.append(", Error Code: ").append(oauth2Error.getErrorCode());
		if (oauth2Error.getDescription() != null) {
			errorDetails.append(", Error Description: ").append(oauth2Error.getDescription());
		}
		errorDetails.append("]");
		oauth2Error = new OAuth2Error(INVALID_USER_INFO_RESPONSE_ERROR_CODE,
				"An error occurred while attempting to retrieve the UserInfo Resource: " + errorDetails.toString(), null);
		throw new OAuth2AuthenticationException(oauth2Error, oauth2Error.toString(), ex);
	} catch (RestClientException ex) {
		OAuth2Error oauth2Error = new OAuth2Error(INVALID_USER_INFO_RESPONSE_ERROR_CODE,
				"An error occurred while attempting to retrieve the UserInfo Resource: " + ex.getMessage(), null);
		throw new OAuth2AuthenticationException(oauth2Error, oauth2Error.toString(), ex);
	}

	// 
	Map<String, Object> userAttributes = response.getBody();
	Set<GrantedAuthority> authorities = new LinkedHashSet<>();
	authorities.add(new OAuth2UserAuthority(userAttributes));
	OAuth2AccessToken token = userRequest.getAccessToken();
	for (String authority : token.getScopes()) {
		authorities.add(new SimpleGrantedAuthority("SCOPE_" + authority));
	}

	return new DefaultOAuth2User(authorities, userAttributes, userNameAttributeName);
}
```