# Insecure Direct Object Reference (IDOR) in Contact Mechanic Functionality

## Severity

High

## CWE

CWE-639 - Authorization Bypass Through User-Controlled Key

## OWASP Mapping

* OWASP API Security Top 10 2023: API1 - Broken Object Level Authorization (BOLA)
* OWASP API Security Top 10 2019: API1 - Broken Object Level Authorization
* OWASP Top 10 2021: A01 - Broken Access Control

## Affected Endpoint

### Frontend Endpoint (Browser Accessible)

```http
/contact-mechanic?VIN={vin}
```

Example:

```http
/contact-mechanic?VIN=69C91V9F3YNY4308K
```

## Description

The application is vulnerable to Insecure Direct Object Reference (IDOR) within the Contact Mechanic functionality.

During testing, it was observed that the application exposes a browser-accessible endpoint where mechanic conversations and vehicle-specific service communication can be accessed by modifying the `VIN` parameter directly in the URL.

The backend fails to verify whether the authenticated user is the rightful owner of the vehicle associated with the provided VIN.

As a result, any authenticated user can access and interact with mechanic conversations belonging to other users.

This vulnerability is exploitable directly from the browser without requiring any specialized interception tools.

## Steps to Reproduce

1. Login using a standard user account.

2. Open the application in the **browser** and navigate to:

```http
/contact-mechanic?VIN=69C91V9F3YNY4308K
```

3. Replace the VIN parameter with another user's VIN directly in the browser URL bar.

4. Press Enter and load the page.

5. Observe that mechanic conversation data belonging to another user is displayed.

6. Send a message using the UI form in the browser.

7. Observe that the message is successfully submitted into another user's mechanic conversation thread.

## Proof of Concept

### Response

evidence/screnshots/IDOR/Contact-Mechanic-IDOR

## Impact

* Unauthorized access to mechanic conversations
* Leakage of vehicle service data
* Message injection into other users’ support threads
* Privacy violation of vehicle owners
* Potential social engineering abuse

### Confidentiality

High

### Integrity

High

### Availability

Low

## Risk Rating

High

## Remediation

* Enforce object-level authorization checks on VIN-based resources
* Ensure only vehicle owners can access associated mechanic conversations
* Validate authorization on every request (server-side only)
* Do not rely on URL parameters for access control decisions
* Apply centralized RBAC for vehicle-related services

## References

* CWE-639 - Authorization Bypass Through User-Controlled Key
  https://cwe.mitre.org/data/definitions/639.html

* OWASP API1:2023 Broken Object Level Authorization
  https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/

* OWASP Authorization Cheat Sheet
  https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
