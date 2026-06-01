# Insufficient Rate Limiting on Login Endpoint

## Severity

High

## CWE

CWE-307 - Improper Restriction of Excessive Authentication Attempts

## OWASP Mapping

* OWASP API Security Top 10 2023: API2 - Broken Authentication
* OWASP Top 10 2021: A07 - Identification and Authentication Failures

## Affected Endpoint

POST /identity/api/auth/login

## Description

The application is vulnerable to insufficient rate limiting and account lockout protection on the authentication endpoint.

During testing, it was observed that the login functionality accepts an unlimited number of authentication attempts without temporarily blocking, throttling, or locking the targeted account.

An attacker can continuously submit password guesses against valid user accounts and perform brute-force attacks against user credentials.

The absence of authentication throttling significantly increases the likelihood of successful credential attacks, especially when weak or reused passwords are present.

## Steps to Reproduce

1. Navigate to the login functionality.
2. Intercept the authentication request using Burp Suite.
3. Observe the login endpoint:

```
POST /identity/api/auth/login
```

4. Configure Burp Intruder or a custom brute-force script.
5. Target a valid account.

Example:

```
attacker@a.com
```

6. Launch a password brute-force attack against the account.
7. Observe repeated authentication failures.
8. Continue sending requests.
9. Observe that the application does not:

   * Lock the account
   * Temporarily block the IP address
   * Introduce increasing delays
   * Require CAPTCHA verification
   * Trigger additional authentication controls
10. Verify that hundreds of authentication attempts can be submitted without restriction.

## Proof of Concept

### Request

Reference:

evidence/requests/CWE-307-2023 API2-login-bruteforce/

### Response

Reference:

evidence/responses/CWE-307-2023 API2-login-bruteforce/

Example Response:

```json
{
  "message":"Invalid credentials"
}
```

### Screenshot

Reference:

evidence/screenshots/CWE-307-2023 API2-login-bruteforce/

## Impact

* User credential brute forcing
* Credential stuffing attacks
* Increased likelihood of account compromise
* Enumeration of weak passwords
* Unauthorized access to user accounts
* Increased risk of automated attacks
* Bypass of expected authentication security controls

Confidentiality: High

Integrity: High

Availability: Low

## Risk Rating

High

## Remediation

* Implement account lockout after a defined number of failed authentication attempts.
* Apply rate limiting on authentication endpoints.
* Introduce progressive delays after repeated failures.
* Deploy CAPTCHA or equivalent challenge mechanisms.
* Monitor and alert on excessive authentication failures.
* Implement IP-based and account-based throttling.
* Require Multi-Factor Authentication (MFA) for sensitive accounts.
* Log and investigate brute-force indicators.

## References

* CWE-307: Improper Restriction of Excessive Authentication Attempts

* https://cwe.mitre.org/data/definitions/307.html

* OWASP API2:2023 Broken Authentication

* https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/

* OWASP Authentication Cheat Sheet

* https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
