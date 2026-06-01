# Methodology

## Overview

The assessment was conducted using a black-box testing approach to simulate the actions of an external attacker with no prior knowledge of the application's internal architecture or source code.

Testing activities were performed using a combination of automated and manual techniques aligned with:

* OWASP Web Security Testing Guide (WSTG)
* OWASP API Security Top 10

---

## Reconnaissance

The initial phase focused on understanding the application's functionality, attack surface, and available API endpoints.

Activities included:

* Manual Application Mapping
* Endpoint Discovery
* Parameter Analysis
* User Role Enumeration
* Functionality Exploration

---

## Automated Testing

The following tools were utilized during the assessment:

* Burp Suite
* OWASP ZAP
* Wapiti
* ffuf
* Katana

Automated scanning assisted with:

* Endpoint Discovery
* Content Enumeration
* Initial Vulnerability Identification
* Attack Surface Mapping

While automated tools provided valuable reconnaissance data, all significant findings were manually validated prior to reporting.

---

## Manual Security Testing

Manual testing focused on identifying vulnerabilities that are often missed by automated scanners.

Activities included:

* Authentication Testing
* Authorization Testing
* JWT Security Testing
* API Security Testing
* Business Logic Testing
* Injection Testing
* Access Control Validation
* Parameter Manipulation
* IDOR/BOLA Testing

---

## Vulnerability Validation

Potential vulnerabilities were manually verified to eliminate false positives and confirm exploitability.

Validation activities included:

* Proof-of-Concept Development
* Impact Verification
* Evidence Collection[Request,Response,Screenshots,Notes,\]
* Risk Assessment
* Remediation Analysis

---

## Reporting

Each confirmed finding was documented with:

* Severity Rating
* CWE Classification
* OWASP Mapping
* Technical Description
* Reproduction Steps
* Evidence
* Impact Assessment
* Remediation Recommendations

---

## Conclusion

The methodology combined automated reconnaissance with extensive manual testing to identify vulnerabilities affecting authentication, authorization, business logic, and API security controls. This approach enabled the discovery of security weaknesses that would not typically be detected through automated scanning alone.
