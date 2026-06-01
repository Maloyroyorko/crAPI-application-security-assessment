# Executive Summary

This repository documents a **7-day Black-Box Vulnerability Assessment and Penetration Testing (VAPT)** engagement conducted against the intentionally vulnerable **crAPI (Completely Ridiculous API)** application.

The assessment was performed using a combination of automated and manual security testing techniques aligned with the **OWASP Web Security Testing Guide (WSTG)** and **OWASP API Security Top 10** methodologies.

## Assessment Overview

* **Assessment Type:** Black-Box VAPT
* **Target Application:** crAPI (Completely Ridiculous API)
* **Assessment Duration:** 7 Days
* **Methodology:** OWASP WSTG, OWASP API Security Top 10
* **Testing Approach:** Automated & Manual Security Testing

## Scope

### In Scope

* Web Application Testing
* REST API Testing
* Authentication Mechanisms
* Authorization Controls
* Business Logic Components
* Vehicle Management Features
* Workshop Services
* Order Management System
* Denial of Service (DoS) testing
* 

### Out of Scope

* Infrastructure Assessment
* Source Code Review
* Operating System Security Assessment
* Third-Party Services
* Social engineering

## Findings Summary

A total of **18 confirmed vulnerabilities** were identified and validated during the assessment.

| Severity | Count |
| -------- | ----- |
| Critical | 2     |
| High     | 14    |
| Medium   | 2     |
| Low      | 0     |

### Key Vulnerability Categories

* Broken Object Level Authorization (BOLA / IDOR)
* Broken Function Level Authorization (BFLA)
* JWT Authentication Weaknesses
* Authentication & Authorization Failures
* SQL Injection
* NoSQL Injection
* Server-Side Request Forgery (SSRF)
* Business Logic Flaws
* Excessive Data Exposure
* Resource Exhaustion / Denial of Service

## Impact Overview

Successful exploitation of identified vulnerabilities could lead to:

* Unauthorized access to sensitive information
* Account takeover
* Administrative function abuse
* Financial manipulation
* Information disclosure
* Unauthorized modification of application data

## Conclusion

The assessment demonstrated several critical weaknesses affecting access control, authentication, input validation, and business logic security. The findings and remediation recommendations provided in this repository are intended to support secure development practices and improve overall application security posture.

> **Disclaimer:** This assessment was conducted against the intentionally vulnerable crAPI application within a controlled laboratory environment for educational and portfolio purposes.
