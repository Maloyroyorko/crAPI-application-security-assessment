# Insufficient Rate Limiting on Password Reset OTP Verification Leading to Account Takeover

## Severity

High

## CWE

CWE-307 - Improper Restriction of Excessive Authentication Attempts

## OWASP Mapping

* OWASP API Security Top 10 2023: API2 - Broken Authentication
* OWASP Top 10 2021: A07 - Identification and Authentication Failures

## Affected Endpoint

POST /identity/api/auth/v2/check-otp

## Description

The application is vulnerable to insufficient rate limiting and brute-force protection within the password reset OTP verification process.

During testing it was observed that the newer endpoint:

```
POST /identity/api/auth/v3/check-otp
```

implements protection mechanisms that block repeated OTP guessing attempts.

However, the legacy endpoint:

```
POST /identity/api/auth/v2/check-otp
```

remains accessible and does not enforce equivalent protections.

An attacker can continuously submit OTP guesses without being rate limited or temporarily blocked, allowing brute-force discovery of valid password reset codes and resulting in unauthorized account takeover.

## Steps to Reproduce

1. Initiate a password reset request for a victim account.

Example:

```
victim@victim.com
```

2. Intercept the OTP verification request.

3. Test the current endpoint:

```
POST /identity/api/auth/v3/check-otp
```

4. Submit multiple invalid OTP values.

5. Observe rate limiting responses:

```json
{
  "message":"You've exceeded the number of attempts.",
  "status":503
}
```

6. Modify the endpoint version:

```
POST /identity/api/auth/v2/check-otp
```

7. Launch an OTP brute-force attack using Burp Suite Intruder.

8. Observe that the endpoint does not enforce rate limiting or account lockout.

9. Continue brute-force attempts until a valid OTP is identified.

10. Successful OTP discovered:

```
3210
```

11. Complete the password reset process.

12. Login using the newly assigned password which you set at intruder.

13. Observe successful access to the victim account.

## Proof of Concept

### Request

Reference:

evidence/requests/CWE-307-2023 API2-otp-bruteforce/

### Response

Reference:

evidence/responses/CWE-307-2023 API2-otp-bruteforce/

Example Response:

```json
{
  "message":"Password updated successfully"
}
```

### Screenshot

Reference:

evidence/screenshots/CWE-307-2023 API2-otp-bruteforce/DAY-2-Test
evidence/screenshots/CWE-307-2023 API2-otp-bruteforce/DAY-6-Retest


## Impact

* Password reset OTP brute forcing
* Authentication bypass
* Unauthorized account access
* Complete account takeover
* Unauthorized password changes
* Exposure of victim account information
* Persistent compromise of user accounts
* Bypass of security controls implemented in newer API versions

Confidentiality: High

Integrity: High

Availability: Low

## Risk Rating

High

## Remediation

* Apply identical rate-limiting controls across all API versions.
* Deprecate and remove insecure legacy authentication endpoints.
* Implement account lockout after repeated OTP failures.
* Enforce OTP expiration and retry limits.
* Add IP-based and account-based throttling.
* Monitor and alert on OTP brute-force activity.
* Conduct security reviews whenever API versions are retained for backward compatibility.

## References

* CWE-307: Improper Restriction of Excessive Authentication Attempts

* https://cwe.mitre.org/data/definitions/307.html

* OWASP API2:2023 Broken Authentication

* https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/

* OWASP Authentication Cheat Sheet

* https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
