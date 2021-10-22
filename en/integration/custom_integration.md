# Custom Integration

##	Authentication via QR code

The XignManager provides two separate endpoints to retrieve the qr code and the authentication result. The communication with these endpoints is abstracted into a JavaScript library.
The endpoint for the QR code is available at:

https://\<xign-system-url\>:\<port\>/api/rp/qrcode

After the QR Code is retrieved and displayed the user performs the authentication, after which the authentication result is retrieved from:

https://\<xign-system-url\>:\<port\>/api/rp/auth-response

sdkarfghfasdhjkgfhjksgadfhjkgsdghjkasdfhjkeasdfghjksadfghjashjkdfghjsdfghjksaf
### Code Example for Browser

To integrate XignIn, there are two steps the integrator has to follow. First the following code snippet has to be integrated into the page, which is responsible for showing the QR code to the user and to control the user agent.

```html
<script type="text/javascript" src="###manager.url###/js/xignqr-jslogin.umd.min.js"></script>

<script type="text/javascript">
    document.addEventListener("DOMContentLoaded", function () {
        const config = {
            managerUri: "###manager.url###",
            containerId: "login",
            clientId: "###client.id###",
            mode: "###client.loginMode###",
            redirectUri: "###client.redirectUri###",
            useSSO: false
        };

        const login = new XignQRLogin.XignQRLogin(config);
        login.start();
    });
</script>

```

### Authentication Flow

<img src="../images/high_level_flow.png" alt="drawing" width="800"/>


## Authentication via push notification

The service provider is able to trigger an authentication process via push-notifications, that are sent to the user’s smartphone.
The push-notification is sent, when the service provider sends an appropriate message to the push authentication endpoint available at:

https://\<xign-system-url\>:\<port\>/api/rp/push

The result of the push notification is retrieved from the push result endpoint at:

https://\<xign-system-url\>:\<port\>/api/rp/push-result

The Authentication via push notifications resembles that of the authentication via QR code. The main difference between the triggers, is the way the information about the authentication process is conveyed to the smartphone.

## Code Example at Redirect URI

After authentication is done (no matter if push or qr code), the user-agent is redirected to the registered redirect URI, along with an authorization code. At the redirect URI the integrator is able to make use of our XignIn server-side library to retrieve, decrypt and verify the information about the user. The following snippet shows the code, that is necessary: 

```js
String code = request.getParameter("code");
InputStream inProps = Redirect.class.getResourceAsStream("/test_config.properties");
TokenFetcherClient tc = new TokenFetcherClient(inProps);
JWTClaimsSet claims = tc.requestIdToken(code);
```

## Token-Endpoint

Available at: https://\<xign-system-url\>:\<port\>/api/rp/token

After the authentication is done, the user’s user-agent is redirected to the registered redirect URI, along with an authorization code. At the redirect URI the service provider uses the authorization code to retrieve information about the authenticated user. The token endpoint is in fact a OpenID Connect Token Endpoint, that supports encryption compliant to the OpenID Connect Specification:


Encryption:
* RSA OAEP (Encryption of a Content Encryption Key with the RSA Public Key of the service provider), the algorithm used is A256CBC-HS512 (Advanced Encryption Standard (AES) in Cipher Block Chaining (CBC) mode with PKCS #5 padding (NIST.800-38A) with an integrity calculation using HMAC SHA-512, using a 512 bit CMK and a 256 bit CEK (see RFC7516)
*	ECDH-ES (Key Agreement based on Elliptic Curve Keys; the resulting key is used to encrypt the CEK) the algorithm used is also A256CBC-HS512 (Advanced Encryption Standard (AES) in Cipher Block Chaining (CBC) mode with PKCS #5 padding (NIST.800-38A) with an integrity calculation using HMAC SHA-512, using a 512 bit CMK and a 256 bit CEK (see RFC7518)

Signatures:
* RS256 RSASSA-PKCS-v1_5 with SHA-256 Hash Algorithm (see RFC7515)
* ES256 ECDSA with curve P-256 (secpr-256) and SHA-256 Hash algorithm (see RFC7515)

The token requests resembles the token requests defined in section 3.1.3.1.  Token Request of the OpenID Connect specification. In response to the token request, the server issues an OpenID Connect ID Token Response containing the ID Token and ultimately the user information.
