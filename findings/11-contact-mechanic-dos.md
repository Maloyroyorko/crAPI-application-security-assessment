\# Uncontrolled Resource Consumption in Contact Mechanic API (Potential DoS Vector)



\## Severity

High



\## CWE

CWE-400 - Uncontrolled Resource Consumption



\## OWASP Mapping

OWASP API Top 10:

\- API4:2023 - Unrestricted Resource Consumption



\---



\## Affected Endpoint

POST /workshop/api/merchant/contact\_mechanic



\---



\## Description



The application exposes a request parameter-based mechanism that allows clients to control retry behavior and execution repetition:



```json

"repeat\_request\_if\_failed": true,

"number\_of\_repeats": <user-controlled>

```



This logic is not properly restricted on the server side.



An attacker can supply extremely large values for `number\_of\_repeats`, causing excessive internal request execution loops.



This leads to a \*\*resource exhaustion risk\*\* and potential \*\*service degradation under malicious input conditions\*\*.



\---



\## Steps to Reproduce



1\. Login as authenticated user

2\. Intercept request to contact mechanic API

3\. Modify payload:



```json

{

&#x20; "mechanic\_code": "TRAC\_JME",

&#x20; "problem\_details": "attack",

&#x20; "vin": "69C91V9F3YNY4308K",

&#x20; "mechanic\_api": "https://merlin-declivitous-crowdedly.ngrok-free.dev/workshop/api/mechanic/receive\_report",

&#x20; "repeat\_request\_if\_failed": true,

&#x20; "number\_of\_repeats": 999999999999999999999999999999999

}

```



4\. Send request



\---



\## Observed Response



```json

{

&#x20; "message": "Service unavailable. Seems like you caused layer 7 DoS :)"

}

```



\---



\## Request Evidence



```

evidence/requests/DOS
```



\---

\## Response Evidence



```

evidence/responses/dos.txt

```

\---

\## Screenshot Evidence



```

evidence/screenshots/DOS

```

\---



\## Impact



\- Potential resource exhaustion on application layer

\- Abuse of internal retry mechanism

\- Possibility of service degradation under repeated abuse

\- Increased load on backend worker services

\- Risk of cascading failures in dependent services



Confidentiality: Low  

Integrity: Low  

Availability: High (Potential)



\---



\## Risk Rating

High



\---



\## Root Cause



\- No validation on `number\_of\_repeats`

\- No maximum threshold enforcement

\- Trusting client-controlled retry logic

\- Lack of rate limiting / circuit breaker pattern



\---



\## Remediation



\- Enforce strict upper limit (e.g. max 3–5 retries)

\- Remove client-controlled retry logic entirely

\- Implement server-side rate limiting

\- Add circuit breaker / queue control system

\- Validate integer bounds on all numeric inputs

\- Reject excessively large values



\---



\## References



\- OWASP API4:2023 - Unrestricted Resource Consumption  

&#x20; https://owasp.org/www-project-api-security/



\- CWE-400: Uncontrolled Resource Consumption  

&#x20; https://cwe.mitre.org/data/definitions/400.html



\- OWASP Denial of Service Prevention Cheat Sheet  

&#x20; https://cheatsheetseries.owasp.org/cheatsheets/Denial\_of\_Service\_Prevention\_Cheat\_Sheet.html

```

