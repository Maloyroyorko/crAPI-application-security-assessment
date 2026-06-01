# Insecure Direct Object Reference (IDOR) in Vehicle Location API

## Severity

High

## CWE

CWE-639 - Authorization Bypass Through User-Controlled Key

## OWASP Mapping

* OWASP API Security Top 10 2023: API1 - Broken Object Level Authorization (BOLA)
* OWASP API Security Top 10 2019: API1 - Broken Object Level Authorization
* OWASP Top 10 2021: A01 - Broken Access Control

## Affected Endpoint

### Backend API Endpoint

```http
GET /identity/api/v2/vehicle/{vehicle_id}/location
```

Example:

```http
GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/location
```

## Description

The application is vulnerable to Insecure Direct Object Reference (IDOR) due to missing object-level authorization checks within the vehicle location functionality.

During testing, it was observed that an authenticated user can access the real-time location information of arbitrary vehicles by modifying the `vehicle_id` parameter in the API request.

The backend validates that the requester is authenticated but fails to verify ownership of the requested vehicle before returning sensitive location information.

As a result, any authenticated user can enumerate vehicle identifiers and retrieve location information belonging to other users.

The vulnerability exposes sensitive information including GPS coordinates, vehicle identifiers, owner information, and associated account details.

## Steps to Reproduce

1. Login using a low-privileged user account.

2. Navigate to the dashboard functionality.

3. Intercept the vehicle location request using Burp Suite.

Example:

```http
GET /identity/api/v2/vehicle/f1cc8f72-8da1-44d5-a197-b23504fe6290/location HTTP/2
Authorization: Bearer <attacker_jwt>
```

4. Modify the vehicle identifier to another user's vehicle.

Example:

```http
GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/location HTTP/2
Authorization: Bearer <attacker_jwt>
```

5. Forward the request.

6. Observe that the application returns location information for a vehicle not owned by the authenticated user.

7. Verify that the response contains another user's vehicle information and location data.

## Proof of Concept

### Request

Reference:

```text
evidence/requests/IDOR/vehicle-location-IDOR.txt
```

Example Request:

```http
GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/location HTTP/2
Host: target
Authorization: Bearer <attacker_jwt>
```

### Response

Reference:

```text
evidence/responses/IDOR/vehicle-location-IDOR.txt
```

Example Response:

```json
{
  "carId":"4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5",
  "vehicleLocation":{
    "id":3,
    "latitude":"37.746880",
    "longitude":"-84.301460"
  },
  "fullName":"Robot",
  "email":"robot001@example.com"
}
```

### Screenshot

Reference:

```text
evidence/screenshots/Vehicle-Location-IDOR/

## Impact

The vulnerability allows authenticated users to access vehicle information belonging to other users without authorization.

Sensitive information exposed includes:

* Vehicle identifiers
* Real-time GPS coordinates
* Vehicle ownership information
* User email addresses
* User profile information

Potential attack scenarios include:

* Vehicle tracking and surveillance
* User privacy violations
* Targeted phishing campaigns
* User enumeration
* Location intelligence gathering
* Chaining with additional BOLA vulnerabilities

### Confidentiality

High

### Integrity

Low

### Availability

Low

## Risk Rating

High

## Remediation

* Implement object-level authorization checks for all vehicle-related resources.
* Verify that the authenticated user owns the requested vehicle before returning data.
* Deny access when the requested vehicle belongs to another account.
* Enforce ownership validation at the API layer.
* Perform centralized authorization validation for vehicle resources.
* Conduct authorization testing on all object-reference endpoints.
* Log and monitor unauthorized access attempts.

Example Pseudocode:

```python
vehicle = get_vehicle(vehicle_id)

if vehicle.owner_id != current_user.id:
    return 403

return vehicle.location
```

## References

* CWE-639 - Authorization Bypass Through User-Controlled Key
  https://cwe.mitre.org/data/definitions/639.html

* OWASP API1:2023 Broken Object Level Authorization
  https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/

* OWASP API1:2019 Broken Object Level Authorization
  https://owasp.org/API-Security/editions/2019/en/0xa1-broken-object-level-authorization/

* OWASP Authorization Cheat Sheet
  https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
