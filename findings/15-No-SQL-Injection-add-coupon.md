# NoSQL Injection in Coupon Validation API

## Severity
High

## CWE
CWE-943: Improper Neutralization of Special Elements in Data Query Logic

## OWASP Mapping
OWASP API Top 10: API8 - Injection

## Affected Endpoint
POST /community/api/v2/coupon/validate-coupon

## Description
The application is vulnerable to NoSQL Injection in the `coupon_code` parameter of the coupon validation API.

The backend improperly processes user-controlled JSON input and directly evaluates it in a database query context without strict type validation or input sanitization. This allows injection of NoSQL operators (e.g., `$ne`) which alters query logic and returns unintended coupon data.

As a result, an attacker can bypass normal coupon validation logic and retrieve valid coupon codes.

## Steps to Reproduce

1. Login as attacker account (attacker@a.com).
2. Navigate to coupon validation functionality.
3. Intercept request using Burp Suite.
4. Send normal request:
   {"coupon_code":"22"}
5. Modify request to inject NoSQL operator:
   {"coupon_code":{"$ne": null}}
6. Observe that valid coupon data is returned.
7. Confirm bypass using alternate payload:
   {"coupon_code":{"$ne": ""}}

## Proof of Concept

### Request
POST /community/api/v2/coupon/validate-coupon

Reference:
### Request
evidence/requests/No-SQLI-Injection/nosqli_coupon_validate.txt

### Response
Reference:
evidence/responses/No-SQLI-Injection/nosqli_coupon_validate.txt

Example Response:
{"coupon_code":"TRAC075","amount":"75","CreatedAt":"2026-05-07T04:57:33.51Z"}

### Screenshot
Reference:
evidence/screenshots/No-SQLI-Injection/nosqli_coupon_validate-1.png
evidence/screenshots/No-SQLI-Injection/nosqli_coupon_validate-2.png

## Impact

- Attacker can bypass coupon validation logic
- Unauthorized access to valid coupon codes
- Financial abuse of discount system
- Exposure of backend coupon dataset structure
- Potential chaining with business logic flaws

## Risk Rating
High

## Remediation

- Enforce strict input validation (only allow string values)
- Reject MongoDB operators such as `$ne`, `$gt`, `$lt`
- Use parameterized query builders
- Validate schema on backend (strong typing)
- Implement allowlist for coupon formats
- Add monitoring for injection patterns

## References
- OWASP API Security Top 10: https://owasp.org/www-project-api-security/
- Portswigger NoSQL Injection: https://portswigger.net/web-security/nosql-injection
