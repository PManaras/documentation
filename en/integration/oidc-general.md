@autoHeader:1.1

# General
## Available Endpoints
With the existing information from point 2, most OpenId Connect configurations can be done. Below are a few more public URLs that may be necessary for a complete OpenId Connect Integration.

**OpenId-Configuration**  
https://xign.me/openid/.well-known/openid-configuration

**Authorization Endpoint**  
https://xign.me/openid/authenticate

**Token Endpoint**  
https://xign.me/api/rp/token

**Issuer**  
https://xign.me

**JWKS Endpoint**  
https://xign.me/openid/jwks

**User Info Endpoint**  
https://xign.me/openid/user


## Supported Authentication Flows

**Authorization Code Flow**  
https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth

**Implicit Flow**  
https://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth

**Hybrid Flow**  
https://openid.net/specs/openid-connect-core-1_0.html#HybridFlowAuth


# Encryption and Signature
## Signatures
**Signaturealgorithms**  
* RS256 (RSASSA-PKCS1-v1_5 mit SHA256)
    * 4096 Bit keylength
* ES256 (ECDSA mit P-256 und SHA-256)
    * secpr256r1 aka prime256v1

## Encryption
**Encryptionalgorithms and Modes**  
* RSA-OAEP mit A256CBC_HS512
    * 4096 Bit keylength
* ECDH-ES mit A256CBC_HS512
    * secpr256r1 aka prime256v1
