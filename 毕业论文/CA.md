# CA 证书中心
信息来源 - wiki
## 证书的申请过程
- 证书申请者将要加密的对象连同主体消息，使用目的等组成证书签署请求，发送至可信的认证机构（中心）
- 认证机构核对（通过其他方式）核查申请者的身份是否可信且对应
- 若认证机构认可申请者的请求，将申请者的待加密信息连和消息主体，证书有效期，用途等限制条件进行组合，将组合成证书最基本的数据
- 认证机构使用自身的私钥对待加密内容加上数字签名并生成**证书**（Tips：并非对加密内容中所有内容进行加密，而是对内容摘要（提取内容中的部分）进行加密，加密后的内容将放入证书中当作数字签名）
- 认证机构将证书发还至申请者手中

## 证书的使用过程
- 申请者可随意将证书对外公布
- 使用证书者信任申请者使用的证书中心，并拥有认证中心的公钥用于验证数字签名
- 如果使用者收获到一份证书，在通过认证中心的公钥对证书的数字签名认证成功则可信证书上的内容。

Tips：如果加密内容为申请者的公钥，则使用者可使用公钥对信息进行加密并通过传递至申请者手中。

## 非对称加密中公钥的使用(Public Key)
- 公钥的用处：可用于加密信息；可用于验证签名。[JWK](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41#section-4.2)
  > The "use" parameter is employed to indicate whether a
   public key is used for encrypting data or verifying the signature on
   data.

## 暂时
6.1.  "kty" (Key Type) Parameter Values

The table below is the set of "kty" (key type) parameter values that are defined by this specification for use in JWKs.
```
+-------------+------------------------------------+----------------+
| kty Param   | Key Type                           | Implementation |
| Value       |                                    | Requirements   |
+-------------+------------------------------------+----------------+
| EC          | Elliptic Curve [DSS]               | Recommended+   |
| RSA         | RSA [RFC3447]                      | Required       |
| oct         | Octet sequence (used to represent  | Required       |
|             | symmetric keys)                    |                |
+-------------+------------------------------------+----------------+
```

The use of "+" in the Implementation Requirements indicates that the requirement strength is likely to be increased in a future version of the specification.
