@autoHeader:1.1

# Allgemein
## Verfügbare Endpunkte
Mit den vorhandenen Informationen aus Punkt 2, können die meisten OpenId Connect Konfigurationen durchgeführt werden. Im Folgenden sind noch ein paar weitere öffentliche URLs, die für eine komplette OpenId-Connect Anbindung notwendig sein könnten.

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


## Unterstützte Authentication Flows

**Authorization Code Flow**  
https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth

**Implicit Flow**  
https://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth

**Hybrid Flow**  
https://openid.net/specs/openid-connect-core-1_0.html#HybridFlowAuth


# Verschlüsselung und Signatur
## Signaturen
**Signaturalgorithmen**  
* RS256 (RSASSA-PKCS1-v1_5 mit SHA256)
    * 4096 Bit Schlüssellänge
* ES256 (ECDSA mit P-256 und SHA-256)
    * secpr256r1 aka prime256v1

## Verschlüsselung
**Verschlüsselungsalgorithmen und Methoden**  
* RSA-OAEP mit A256CBC_HS512
    * 4096 Bit Schlüssellänge
* ECDH-ES mit A256CBC_HS512
    * secpr256r1 aka prime256v1
