# Insecure Direct Object Reference (IDOR) in Vehicle Service Dashboard

---

## 1. Vulnerability Overview

| Field                | Value                                                     |
| -------------------- | --------------------------------------------------------- |
| Severity             | High                                                      |
| CWE                  | CWE-639: Authorization Bypass Through User-Controlled Key |
| OWASP API Mapping    | API1: Broken Object Level Authorization (BOLA)            |
| OWASP Top 10 Mapping | A01: Broken Access Control                                |
| Affected Endpoint    | GET /vehicle-service-dashboard                            |
| Parameter            | VIN                                                       |

---

## 2. Description

The application exposes both a browser-based endpoint and an underlying API endpoint for retrieving vehicle service information. The vulnerability exists at the API layer, which is directly consumed by the frontend dashboard.

The browser endpoint `/vehicle-service-dashboard` triggers an API call to `/workshop/api/merchant/service_requests/{VIN}`. The API uses a user-controlled `VIN` parameter to retrieve vehicle service records without enforcing object-level authorization.

As a result, an authenticated user can access service requests and vehicle ownership data belonging to other users by modifying the VIN value.

This represents a Broken Object Level Authorization (BOLA) vulnerability where server-side access control checks are missing at the API layer.

The Vehicle Service Dashboard endpoint is vulnerable to Insecure Direct Object Reference (IDOR), resulting in Broken Object Level Authorization (BOLA).

The application uses a user-controlled parameter `VIN` (Vehicle Identification Number) to retrieve service history data. However, the backend does not validate whether the authenticated user is authorized to access the requested vehicle resource.

As a result, an authenticated user can access vehicle service records belonging to other users by modifying the VIN parameter value.

This represents a direct failure of object-level authorization enforcement on the server side.

---

## 3. Steps to Reproduce

1. Authenticate into the application using a standard user account.

   Example:

   ```
   attacker@a.com
   ```

2. Intercept or identify the backend API request triggered by the dashboard.

   Example API endpoint:

   ```
   GET /workshop/api/merchant/service_requests/<VIN> HTTP/2
   ```

3. Modify the VIN parameter in the API request to a value belonging to another user.

4. Send the modified request and observe the response.

5. Confirm that service request data is returned for a vehicle not owned by the authenticated user.

6. Authenticate into the application using a standard user account.

   Example:

   ```
   attacker@a.com
   ```

7. Identify or obtain a valid VIN belonging to another user.

   Example:

   ```
   69C91V9F3YNY4308K
   ```

8. Send a request to the Vehicle Service Dashboard endpoint:

   ```
   GET /vehicle-service-dashboard?VIN=<VIN>   ```

9. Observe that the response returns service history data for the specified VIN.

10. Confirm that the data corresponds to a vehicle not owned by the authenticated user.

---

## 4. Proof of Concept

### 4.1 HTTP Request (API Layer)

The vulnerability is present at the backend API layer invoked by the dashboard.

```http
GET /workshop/api/merchant/service_requests/<VIN> HTTP/2
Host: target-application
Authorization: Bearer <valid_token>
Cookie: <session_cookie>
```

```http
GET /vehicle-service-dashboard?VIN=<VIN> HTTP/2
Host: target-application
Cookie: session=<valid_session_token>
```

---

### 4.2 HTTP Response (Sample)

The API returns service request data including vehicle ownership information without authorization checks.

````json

```html
Vehicle Service History

VIN: 69C91V9F3YNY4308K

Service Records:
- Oil Change
- Tire Replacement
- Brake Inspection
````

---

### 4.3 Evidence References

* Requests: `evidence/requests/IDOR/vehicle-service-history-idor.txt`
* Responses: `evidence/responses/IDOR/vehicle-service-history-idor.txt`
* Screenshots: `evidence/screenshots/IDOR/Vehicle-Service-History`

---

## 5. Impact

This vulnerability allows an authenticated user to access unauthorized vehicle service records.

### Security Impact:

* Unauthorized access to vehicle service history
* Exposure of vehicle ownership information
* Disclosure of maintenance and service records
* Potential privacy violations of customer data
* Ability to enumerate valid VINs
* Potential chaining with other IDOR vulnerabilities

### CIA Impact:

* Confidentiality: High
* Integrity: Low
* Availability: Low

---

## 6. Risk Rating

**High**

---

## 7. Remediation Recommendations

* Implement strict server-side object-level authorization checks
* Validate VIN ownership against the authenticated user session
* Never rely on client-controlled identifiers for access control decisions
* Return HTTP 403 Forbidden for unauthorized access attempts
* Centralize authorization logic for all vehicle-related endpoints
* Conduct security testing for all object-referencing parameters
* Implement proper access control middleware for API endpoints

---

## 8. References

* CWE-639: Authorization Bypass Through User-Controlled Key
  [https://cwe.mitre.org/data/definitions/639.html](https://cwe.mitre.org/data/definitions/639.html)

* OWASP API1:2023 Broken Object Level Authorization (BOLA)
  [https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)

* OWASP Top 10: A01 Broken Access Control
  [https://owasp.org/Top10/A01_2021-Broken_Access_Control/](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

* OWASP Authorization Cheat Sheet
  [https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
