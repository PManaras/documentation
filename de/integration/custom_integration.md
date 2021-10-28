# Custom Integration

##	Authentication via QR code

Der XignManager bietet zwei separate Endpunkte, um den QR-Code und das Authentifizierungsergebnis abzurufen. Die Kommunikation mit diesen Endpunkten ist in einer JavaScript-Bibliothek abstrahiert.
Der Endpunkt für den QR-Code ist verfügbar unter:

https://\<xign-system-url\>:\<port\>/api/rp/qrcode

Nachdem der QR-Code abgerufen und angezeigt wurde, führt der Benutzer die Authentifizierung durch, woraufhin das Authentifizierungsergebnis abgerufen wird:

https://\<xign-system-url\>:\<port\>/api/rp/auth-response


### Code Example for Browser

Um XignIn zu integrieren, muss der Integrator zwei Schritte befolgen. Zunächst muss das folgende Code-Snippet in die Seite integriert werden, die für die Anzeige des QR-Codes und die Steuerung des User-Agents verantwortlich ist.

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

<img src="https://raw.githubusercontent.com/PManaras/documentation/master/_media/high_level_flow.png" alt="drawing" width="800"/>


## Authentication via push notification

Der Dienstanbieter kann einen Authentifizierungsprozess über Push-Benachrichtigungen auslösen, die an das Smartphone des Nutzers gesendet werden.
Die Push-Benachrichtigung wird gesendet, wenn der Dienstanbieter eine entsprechende Nachricht an den Push-Authentifizierungsendpunkt sendet, der unter folgender Adresse verfügbar ist:

https://\<xign-system-url\>:\<port\>/api/rp/push

Das Ergebnis der Push-Benachrichtigung wird vom Push-Ergebnis-Endpunkt unter abgerufen:
https://\<xign-system-url\>:\<port\>/api/rp/push-result

Die Authentifizierung über Push-Benachrichtigungen ähnelt der Authentifizierung über QR-Code. Der Hauptunterschied zwischen den Auslösern ist die Art und Weise, wie die Informationen über den Authentifizierungsvorgang an das Smartphone übermittelt werden.

## Code Example at Redirect URI

Nach erfolgter Authentifizierung (egal ob Push oder QR-Code) wird der User-Agent zusammen mit einem Autorisierungscode an die registrierte Redirect-URI weitergeleitet. An der Redirect-URI kann der Integrator unsere serverseitige XignIn-Bibliothek nutzen, um die Informationen über den Benutzer abzurufen, zu entschlüsseln und zu verifizieren. Das folgende Snippet zeigt den dafür notwendigen Code: 

```js
String code = request.getParameter("code");
InputStream inProps = Redirect.class.getResourceAsStream("/test_config.properties");
TokenFetcherClient tc = new TokenFetcherClient(inProps);
JWTClaimsSet claims = tc.requestIdToken(code);
```

## Token-Endpoint

Verfügbar an: https://\<xign-system-url\>:\<port\>/api/rp/token

Nach erfolgter Authentifizierung wird der User-Agent des Benutzers zusammen mit einem Autorisierungscode an die registrierte Redirect-URI weitergeleitet. Am Redirect-URI verwendet der Dienstanbieter den Autorisierungscode, um Informationen über den authentifizierten Benutzer abzurufen. Der Token-Endpunkt ist in der Tat ein OpenID Connect Token-Endpunkt, der die Verschlüsselung gemäß der OpenID Connect-Spezifikation unterstützt:

Verschlüsselung:
* RSA OAEP (Verschlüsselung eines Inhaltsverschlüsselungsschlüssels mit dem öffentlichen RSA-Schlüssel des Dienstanbieters), der verwendete Algorithmus ist A256CBC-HS512 (Advanced Encryption Standard (AES) im Cipher Block Chaining (CBC)-Modus mit PKCS #5 Padding (NIST.800-38A) mit einer Integritätsberechnung unter Verwendung von HMAC SHA-512, unter Verwendung eines 512-Bit CMK und eines 256-Bit CEK (siehe RFC7516)
* ECDH-ES (Key Agreement based on Elliptic Curve Keys; der daraus resultierende Schlüssel wird zur Verschlüsselung des CEK verwendet), der verwendete Algorithmus ist ebenfalls A256CBC-HS512 (Advanced Encryption Standard (AES) im Cipher Block Chaining (CBC)-Modus mit PKCS #5 Padding (NIST.800-38A) mit einer Integritätsberechnung unter Verwendung von HMAC SHA-512, mit einem 512-Bit-CMK und einem 256-Bit-CEK (siehe RFC7518)

Signaturen:
* RS256 RSASSA-PKCS-v1_5 mit SHA-256-Hash-Algorithmus (siehe RFC7515)
* ES256 ECDSA mit Kurve P-256 (secpr-256) und SHA-256-Hash-Algorithmus (siehe RFC7515)

Die Token-Anforderungen ähneln den in Abschnitt 3.1.3.1 definierten Token-Anforderungen.  Token Request der OpenID Connect-Spezifikation. Als Antwort auf die Token-Anforderung gibt der Server eine OpenID Connect ID-Token-Antwort aus, die das ID-Token und schließlich die Benutzerinformationen enthält.
