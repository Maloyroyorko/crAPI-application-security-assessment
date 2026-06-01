# Excessive Data Exposure in Community APIs

## Severity

Medium

## CWE

CWE-213 - Exposure of Sensitive Information Due to Incompatible Policies

## OWASP Mapping

* OWASP API Security Top 10 2019: API3 - Excessive Data Exposure
* OWASP API Security Top 10 2023: API3 - Broken Object Property Level Authorization (BOPLA)

## Affected Endpoints

* GET /community/api/v2/community/posts/recent?limit=30&offset=0
* GET /community/api/v2/community/posts/{post_id}

## Description

The application is vulnerable to Excessive Data Exposure within the Community module.

During testing, it was observed that multiple Community API endpoints disclose sensitive user information that is not required for normal application functionality.

Both the recent posts endpoint and individual post details endpoint expose excessive author metadata, including personally identifiable information (PII) and internal application identifiers.

The API responses disclose:

* User email addresses
* Vehicle identifiers
* User profile metadata
* Internal user attributes
* Comment author information

This information is returned to any authenticated user accessing community content.

The disclosed vehicle identifiers can subsequently be leveraged to facilitate further attacks against vehicle-related functionality, including object enumeration and exploitation of authorization weaknesses.

The application violates the principle of least privilege by exposing significantly more information than required to render community posts and comments.

## Steps to Reproduce

### Community Feed Exposure

1. Login using a standard user account.
2. Navigate to the Community / Forum section.
3. Intercept the request using Burp Suite.
4. Observe the request:

```http
GET /community/api/v2/community/posts/recent?limit=30&offset=0
```

5. Forward the request.
6. Observe sensitive information within the response.

Example:

```json
{
  "author": {
    "nickname": "Victim",
    "email": "victim@victim.com",
    "vehicleid": "f1cc8f72-8da1-44d5-a197-b23504fe6290",
    "profile_pic_url": ""
  }
}
```

### Community Post Details Exposure

1. Open any community post.
2. Intercept the request:

```http
GET /community/api/v2/community/posts/{post_id}
```

3. Forward the request.
4. Inspect the response.
5. Observe disclosure of author and commenter information.

Example:

```json
{
  "comments": [
    {
      "content": "sample comment",
      "author": {
        "nickname": "Attacker",
        "email": "attacker@a.com",
        "vehicleid": ""
      }
    }
  ]
}
```

6. Observe that email addresses and vehicle identifiers are disclosed despite not being required for displaying forum content.

## Proof of Concept

### Request

Reference:

evidence/requests/Excessive-Data-Exposure/

### Response

Reference:

evidence/responses/Excessive-Data-Exposure/

### Example Response

```json
{
  "author": {
    "nickname": "Victim",
    "email": "victim@victim.com",
    "vehicleid": "f1cc8f72-8da1-44d5-a197-b23504fe6290"
  }
}
```

```json
{
  "comments": [
    {
      "author": {
        "nickname": "Attacker",
        "email": "attacker@a.com",
        "vehicleid": ""
      }
    }
  ]
}
```

### Screenshot

Reference:

evidence/screenshots/Excessive-Data-Exposure/

## Impact

* Exposure of user email addresses
* Exposure of internal vehicle identifiers
* Disclosure of commenter information
* Disclosure of author information
* User privacy violations
* Increased attack surface for further attacks
* Facilitation of object enumeration attacks
* Facilitation of Broken Object Level Authorization (BOLA) exploitation
* Leakage of unnecessary internal application data

Confidentiality: Medium

Integrity: Low

Availability: None

## Risk Rating

Medium

## Remediation

* Return only properties required by the frontend application.
* Remove email addresses from public API responses.
* Remove vehicle identifiers from public API responses.
* Implement response filtering and field-level authorization controls.
* Apply the principle of least privilege to all API responses.
* Review all Community API serializers and response objects for unnecessary data exposure.
* Perform regular API response reviews to identify sensitive information leakage.

Example:

Instead of:

```json
{
  "nickname":"Victim",
  "email":"victim@victim.com",
  "vehicleid":"f1cc8f72-8da1-44d5-a197-b23504fe6290"
}
```

Return:

```json
{
  "nickname":"Victim"
}
```

## References

* CWE-213: Exposure of Sensitive Information Due to Incompatible Policies

* https://cwe.mitre.org/data/definitions/213.html

* OWASP API3:2019 Excessive Data Exposure

* https://owasp.org/API-Security/editions/2019/en/0xa3-excessive-data-exposure/

* OWASP API3:2023 Broken Object Property Level Authorization (BOPLA)

* https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/
