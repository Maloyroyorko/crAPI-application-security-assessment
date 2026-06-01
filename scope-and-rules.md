# Scope and Rules of Engagement

## Assessment Overview

This assessment was conducted against the **crAPI (Completely Ridiculous API)** application using a black-box testing methodology.

The objective was to identify, validate, and document security vulnerabilities affecting the application's web and API components while operating within a controlled laboratory environment.

---

## In-Scope Components

The following components were included in the assessment:

### Web Application

* User Registration
* User Authentication
* Account Management
* Community Features
* Workshop Features
* Vehicle Management Functions

### API Components

* REST API Endpoints
* Authentication APIs
* Authorization Mechanisms
* User Management APIs
* Service Management APIs
* Order Management APIs

### Security Controls

* Authentication Controls
* Authorization Controls
* Access Control Mechanisms
* Session Management
* JWT Implementations
* Business Logic Enforcement

---

## Out-of-Scope Components

The following areas were excluded from testing:

* Source Code Review
* Operating System Assessment
* Infrastructure Security Testing
* Network Security Assessment
* Third-Party Services
* External Dependencies
* Social Engineering Activities
* Physical Security Testing

---

## Testing Rules

The assessment followed the following rules of engagement:

### Permitted Activities

* Manual Security Testing
* Automated Vulnerability Scanning
* API Security Assessment
* Authentication Testing
* Authorization Testing
* Business Logic Testing
* Injection Testing
* Access Control Validation
* Proof-of-Concept Exploitation

### Restricted Activities

* Permanent Data Modification
* Destructive Testing
* Malware Deployment
* Attacks Against Third-Party Services

---

## Testing Assumptions

The assessment assumed the perspective of an external attacker with:

* No source code access
* No administrative privileges
* No prior knowledge of internal application architecture
* Access limited to publicly exposed application functionality

---

## Methodology References

Testing activities were aligned with:

* OWASP Web Security Testing Guide (WSTG)
* OWASP API Security Top 10
* Industry-Standard Penetration Testing Practices

---

## Disclaimer

This assessment was performed exclusively within a controlled laboratory environment using the intentionally vulnerable crAPI application for educational, research, and portfolio development purposes.

No production systems, real customer data, or third-party environments were targeted during this assessment.
