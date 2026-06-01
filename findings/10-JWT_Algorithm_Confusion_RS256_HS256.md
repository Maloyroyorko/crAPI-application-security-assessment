# JWT Algorithm Confusion Leading to Authentication Bypass

## Severity

Critical

## CWE

CWE-347 - Improper Verification of Cryptographic Signature

## OWASP Mapping

* OWASP API Security Top 10 2023: API2 - Broken Authentication
* OWASP Top 10 2021: A07 - Identification and Authentication Failures

## Affected Endpoint

### Root Cause Component

POST /identity/api/auth/verify

### Verified Vulnerable Endpoint

* GET /identity/api/v2/user/dashboard

## Description

The application is vulnerable to JWT Algorithm Confusion due to improper validation of the JWT signing algorithm during token verification.

During testing, the application's public RSA verification key was disclosed through the JWKS endpoint:

```text
/.well-known/jwks.json
```

The exposed public key was converted into PEM format and subsequently used as the secret key for an HS256-signed JWT token.

The backend incorrectly accepted the forged HS256 token despite the application being configured to use RS256 asymmetric signatures.

This behavior indicates that the authentication component trusts the algorithm specified within the JWT header rather than enforcing a server-side algorithm whitelist.

As a result, an attacker can forge arbitrary JWT tokens and impersonate other users without possession of the legitimate private signing key.

## Steps to Reproduce

1. Access the publicly available JWKS endpoint:

```text
/.well-known/jwks.json
```

2. Obtain the RSA public key information.
3. Convert the JWK into PEM format.
4. Create a new JWT token using Burp Suite JWT Editor.
5. Modify the JWT header:

```json
{
  "alg":"HS256"
}
```

6. Modify the payload to impersonate another user:

```json
{
  "sub":"victim@victim.com",
  "iat":1779763295,
  "exp":1780368095,
  "role":"user"
}
```

7. Use the extracted RSA public key as the HMAC secret.
8. Sign the token using HS256.
9. Replace the Authorization header with the forged token.
10. Send the request to:

```text
GET /identity/api/v2/user/dashboard
```

11. Observe successful authentication as the victim account.

## Proof of Concept

### Request

Reference:

evidence/requests/JWT-Attack/Alg-Confusion-Attack/

### Response

Reference:

evidence/responses/JWT-Attack/Alg-Confusion-Attack/

Example Response:

```json
{
  "id":8,
  "name":"Victim",
  "email":"victim@victim.com",
  "number":"+01999888777",
  "available_credit":100.0,
  "role":"ROLE_USER"
}
```

### Screenshot

Reference:

evidence/screenshots/JWT-Attack/Alg-Confusion-Attack/

Evidence collected includes:

* JWKS public key disclosure
* Public key conversion to PEM
* HS256 forged token generation
* Dashboard access using forged token
* Victim account impersonation

## Impact

* Authentication bypass
* Horizontal privilege escalation
* Unauthorized access to victim accounts
* Exposure of sensitive account information
* Impersonation of arbitrary users
* Loss of trust in JWT authentication mechanism

Confidentiality: High

Integrity: High

Availability: Low

## Risk Rating

Critical

## Remediation

* Enforce a strict server-side algorithm whitelist.
* Never trust the algorithm supplied in the JWT header.
* Configure the JWT library to only accept the intended algorithm (RS256).
* Reject tokens signed using unexpected algorithms.
* Separate asymmetric and symmetric verification logic.
* Review all authentication middleware for algorithm confusion vulnerabilities.
* Perform regression testing after remediation.

Example:

```javascript
jwt.verify(token, publicKey, {
    algorithms: ["RS256"]
});
```

## References

* CWE-347 - Improper Verification of Cryptographic Signature

* https://cwe.mitre.org/data/definitions/347.html

* OWASP API Security Top 10 2023 - API2 Broken Authentication

* https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/

* PortSwigger JWT Algorithm Confusion

* https://portswigger.net/web-security/jwt/algorithm-confusion
