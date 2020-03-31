# Tips of JWS and JWK and OpenId connect
## OpenID Connect

### Successful Token Response
```json
HTTP/1.1 200 OK
  Content-Type: application/json
  Cache-Control: no-store
  Pragma: no-cache

  {
   "access_token": "SlAV32hkKG",
   "token_type": "Bearer",
   "refresh_token": "8xLOxBtZp8",
   "expires_in": 3600,
   "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
     yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
     NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
     fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
     AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
     Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
     NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
     QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
     K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
     XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
  }
```
idtoken封装在token response中

### 0. UserInfo endpoint
通过Access Token 对认证服务器的user info endpointi进行访问，认证access token后获得对应的用户信息


### 1. ID Token Data Struct
- 客户端向业务服务器发送ID Token，业务服务器通过验证ID Token返回相对应的信息。
- [Data Struct](https://openid.net/specs/openid-connect-core-1_0.html#IDToken)
  - `iss`: 发行人，issuer identifier，认证服务的唯一标识，内容为http URL
  - `sub`: Subject主体，iss提供的终端用户标识(end user)
  - `aud`: Audience听众，本属性内容必须拥有OAuth2 client_id，可能会包含其他audience的identifier
  - `exp`: 期限，超出属性中的时间ID Token将不被接纳accept
  - `nonce`: 将ID Token与Client Session相连接的属性，此属性值用于给第三方客户端通过与session cookie(客户端)的hash对比对ID Token进行验证。
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
        # 302告诉user-agent需要重定向
        HTTP/1.1 302 Found
        Location: https://client.example.org/cb?
          code=SplxlOBeZQQYbYS6WxSbIA // parameter "code" include Authorization code and ID Token
          &state=af0ifjsldkj  
        ```
       

### 2. OpenID Connect 




