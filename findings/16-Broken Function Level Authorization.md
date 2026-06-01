# Broken Function Level Authorization (BFLA) in Administrative Video Management API

## Severity

High

## CWE

CWE-285 - Improper Authorization

## OWASP Mapping

* OWASP API Security Top 10 2023: API5 - Broken Function Level Authorization
* OWASP Top 10 2021: A01 - Broken Access Control

## Affected Endpoint

DELETE /identity/api/v2/admin/videos/{video_id}

## Description

The application is vulnerable to Broken Function Level Authorization (BFLA) within the administrative video management functionality.

During testing, it was observed that a regular authenticated user could access an administrative API endpoint intended for privileged users by directly modifying the URL path from a user endpoint to an admin endpoint.

The application exposes an administrative route:

```
/identity/api/v2/admin/videos/{video_id}
```

However, the backend fails to validate whether the authenticated user possesses administrative privileges before executing sensitive operations.

As a result, a low-privileged user can perform administrative actions against arbitrary video resources.

The issue appears to stem from missing server-side role validation on the administrative controller.

## Steps to Reproduce

1. Login using a standard user account.
2. Upload a video or navigate to the video management functionality.
3. Intercept the request using Burp Suite.
4. Observe a legitimate request:

```
PUT /identity/api/v2/user/videos/52
```

Request Body:

```json
{
  "videoName":"king.mp4"
}
```

5. Identify another valid video ID belonging to a different account.

Example:

```
102
```

6. Modify the endpoint path from:

```
/identity/api/v2/user/videos/102
```

to:

```
/identity/api/v2/admin/videos/102
```

7. Send a PUT request.

8. Observe the response:

```
HTTP/2 405 Method Not Allowed
Allow: DELETE
```

9. Change the HTTP method from PUT to DELETE.

10. Forward the request.

11. Observe successful execution of the administrative action.

Response:

```json
{
  "message":"User video deleted successfully.",
  "status":200
}
```

12. Verify that the targeted video has been deleted despite the attacker lacking administrative privileges.

## Proof of Concept

### Request

Reference:

evidence/requests/Broken Function level Authorization/poc-video-delete.txt

### Response

Reference:

evidence/responses/Broken Function level Authorization/poc-video-delete.txt

evidence/screenshots/Broken Function level Authorization/

Example Response:

```json
{
  "message":"User video deleted successfully.",
  "status":200
}
```

### Screenshot

Reference:

evidence/screenshots/Broken Function level Authorization/

## Impact

* Unauthorized access to administrative functionality
* Privilege escalation from User to Admin-level actions
* Unauthorized deletion of user content
* Loss of integrity of stored video resources
* Potential abuse of additional hidden administrative endpoints
* Increased attack surface due to predictable administrative routing

Confidentiality: Low

Integrity: High

Availability: Medium

## Risk Rating

High

## Remediation

* Implement strict server-side Role-Based Access Control (RBAC) checks.
* Validate user roles before executing any administrative functionality.
* Apply authorization middleware consistently across all administrative routes.
* Deny access to administrative endpoints for non-administrative users.
* Avoid relying on URL structure alone to determine authorization.
* Conduct authorization testing on all privileged API endpoints.
* Remove unnecessary disclosure of allowed methods in error responses where possible.

## References

* CWE-285: Improper Authorization

* https://cwe.mitre.org/data/definitions/285.html

* OWASP API5:2023 Broken Function Level Authorization

* https://owasp.org/API-Security/editions/2023/en/0xa5-broken-function-level-authorization/

* OWASP Access Control Cheat Sheet

* https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
