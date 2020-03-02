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
