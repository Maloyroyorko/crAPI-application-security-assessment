# JWT Signature Validation Bypass via "none" Algorithm

## Severity

Critical

## CWE

CWE-345 - Insufficient Verification of Data Authenticity

## OWASP Mapping

* OWASP API Security Top 10 2023: API2 - Broken Authentication
* OWASP Top 10 2021: A07 - Identification and Authentication Failures

## Affected Endpoint

### Root Cause Component

POST /identity/api/auth/verify

### Verified Vulnerable Endpoints

#### Identity & Account Management

* GET /identity/api/v2/user/dashboard
* POST /identity/api/v2/user/change-password
* PUT /identity/api/v2/user/update-email
* PUT /identity/api/v2/user/update-phone
* PUT /identity/api/v2/user/videos/{id}
* GET /identity/api/v2/vehicle/{vehicle_id}/location

#### Workshop & E-Commerce

* POST /workshop/api/shop/orders
* GET /workshop/api/shop/orders/{order_id}
* GET /workshop/api/mechanic/mechanic_report?report_id={id}
* POST /workshop/api/merchant/contact_mechanic
* POST /workshop/api/shop/apply_coupon

#### Community

* GET /community/api/v2/community/posts/recent
* GET /community/api/v2/community/posts/{post_id}
* POST /community/api/v2/community/post [To post as other account]
* POST /community/api/v2/community/posts/{post_id}/comment [To comment as other account]

## Description

The application is vulnerable to JWT Signature Validation Bypass through acceptance of the `"alg":"none"` algorithm.

The root cause exists within the centralized JWT validation mechanism responsible for authenticating Bearer tokens before granting access to protected resources. Testing identified that the JWT verification component accepts unsigned JWT tokens when the algorithm field is changed to `"none"` and the signature portion is removed entirely.

An attacker can forge arbitrary JWT claims, impersonate any user account, and gain unauthorized access to authenticated functionality without possession of a valid cryptographic signature.

Because authentication is centrally enforced through the shared verification layer, every endpoint relying on JWT authentication inherits the vulnerability.

## Steps to Reproduce

1. Login using a legitimate user account.
2. Capture a valid JWT token from an authenticated request.
3. Decode the token using Burp Suite JWT Editor.
4. Modify the JWT header:

```json
{
  "alg": "none"
}
```

5. Modify the payload to impersonate another user:

```json
{
  "sub":"victim@victim.com",
  "iat":1779763295,
  "exp":1780368095,
  "role":"user"
}
```

6. Remove the JWT signature entirely.
7. Construct the forged token:

```text
eyJhbGciOiJub25lIn0.eyJzdWIiOiJ2aWN0aW1AdmljdGltLmNvbSIsImlhdCI6MTc3OTc2MzI5NSwiZXhwIjoxNzgwMzY4MDk1LCJyb2xlIjoidXNlciJ9.
```

8. Replace the Authorization header with the forged token.
9. Send the request to a protected endpoint.
10. Observe successful authentication as the victim account.

## Proof of Concept

### Request

Reference:

evidence/requests/JWT-Attack/JWT-None-Algorithm/

### Response

Reference:

evidence/responses/JWT-Attack/JWT-None-Algorithm/

Example Response:

```json
{
  "id": 8,
  "name": "Victim",
  "email": "victim@victim.com",
  "number": "+01999888777",
  "available_credit": 100.0,
  "role": "ROLE_USER"
}
```

### Screenshot

Reference:

evidence/screenshots/JWT-Attack/JWT-None-Algorithm/

Evidence collected includes:

* JWT token modification using `"alg":"none"`
* Forged victim token generation
* Victim dashboard access
* Unwanted posting from Victim Account
* Unwanted ordering from Victim Account
* Unwanted commenting from Victim Account
* Password change using forged token
* Email update using forged token
* Phone number update using forged token
* Vehicle location access using forged token
* Additional authenticated endpoint access using forged token

## Impact

* Authentication bypass
* Complete account takeover
* Unauthorized password changes
* Unauthorized email modifications
* Unauthorized/Unwanted posts and comments and order and contact messages to mechanics
* Unauthorized phone number modifications
* Persistent account hijacking
* Access to victim personal information
* Access to vehicle information and GPS data
* Access to workshop and order information
* Ability to perform actions as arbitrary users
* Potential compromise of all JWT-protected functionality

Confidentiality: High

Integrity: High

Availability: Low

## Risk Rating

Critical

## Remediation

* Explicitly reject JWT tokens using the `"none"` algorithm.
* Enforce a strict server-side algorithm whitelist.
* Verify JWT signatures before processing claims.
* Never trust the algorithm specified by the client.
* Reject unsigned JWT tokens.
* Validate issuer, audience, expiration, and signature before granting access.
* Implement centralized authentication hardening and regression testing across all services.
* Review all endpoints relying on the shared authentication component after remediation.

Example:

```javascript
jwt.verify(token, publicKey, {
    algorithms: ["RS256"]
});
```

## References

* CWE-345 - Insufficient Verification of Data Authenticity

* https://cwe.mitre.org/data/definitions/345.html

* OWASP API Security Top 10 2023 - API2 Broken Authentication

* https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/

* OWASP JWT Cheat Sheet

* https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
