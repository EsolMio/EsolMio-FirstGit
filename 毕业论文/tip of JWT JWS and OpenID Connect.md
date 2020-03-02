# Tips of JWS and JWK and OpenId connect
## OpenID Connect
### 1. ID Token Data Struct
- [Data Struct](https://openid.net/specs/openid-connect-core-1_0.html#IDToken)
  - `iss`: 发行人，issuer identifier
  - `sub`: Subject主体，与iss一同，唯一且不再重新分配
  - `aud`: Audience听众，本属性内容必须拥有OAuth2 client_id，可能会包含其他audience的identifier
  - `exp`: 期限，超出属性中的时间ID Token将不被接纳accept
  - `nonce`: 将ID Token与Client Session相连接的属性，此属性值用于给第三方客户端通过与session cookie的hash对比对ID Token进行验证。
- Example:
  ```json
  {
   "iss": "https://server.example.com",
   "sub": "24400320",
   "aud": "s6BhdRkqt3",
   "nonce": "n-0S6_WzA2Mj",
   "exp": 1311281970,
   "iat": 1311280970,
   "auth_time": 1311280969,
   "acr": "urn:mace:incommon:iap:silver"
  }
  ```   
- 获得ID Token的request中scope属性必须包含`openid`，否则仅为单纯的OAuth2 request而非OpenID request

### 对不同`response_type`的响应
[Example](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationExamples)

Tips：
- Authentication using the Authorization Code flow ( `response_type = code` ):
  1. Authentication Request parameters claim:
     1. `scope`: `openid` 如果需要将Authorizaiton Request 同时为 OpenID Request，需要将此参数contain`openid`
     2. `response_type, ... `:  Authentication request 同 OAuth2.0 Authorization Request 相结合，属性设置相同。即需要的参数均为 `response_type`, `scope`, `client_id`, `state`, `redirect_uri` 共5个参数。`response_type` **MUST** 设置为 `code`。
        > 关于ID Token Response（暂时记录 TODO）: an ID Token is returned from the Token Endpoint in response to a Token Request using an Authorization Code
        ```
        Example:
        Request:
        HTTP/1.1 302 Found
        Location: https://server.example.com/authorize?
          response_type=code
          &scope=openid%20profile%20email
          &client_id=s6BhdRkqt3
          &state=af0ifjsldkj
          &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb

        Response:
        HTTP/1.1 302 Found
        Location: https://client.example.org/cb?
          code=SplxlOBeZQQYbYS6WxSbIA // parameter "code" include Authorization code and ID Token
          &state=af0ifjsldkj  
        ```
       

### 2. OpenID Connect 




