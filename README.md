# crAPI-application-security-assessment
7-day black-box web application and API security assessment of crAPI following OWASP WSTG and OWASP API Security Top 10 methodologies. Identified and validated 18 security findings across authentication, authorization, API security, business logic, and injection attack surfaces, supported by evidence collection and professional reporting.

# crAPI Vulnerability Assessment & Penetration Testing (VAPT)

## 📌 Overview

This project documents a **7-day black-box Vulnerability Assessment and Penetration Testing (VAPT)** conducted on the deliberately vulnerable application **crAPI (Completely Ridiculous API)**.

The assessment focuses on identifying real-world API and web application security flaws using industry-standard methodologies, primarily aligned with:

- OWASP Web Security Testing Guide (WSTG)
- OWASP API Security Top 10

A total of **18 validated security findings** were identified and documented with proof-of-concept evidence, impact analysis, and remediation guidance.

---

## 🎯 Objectives

- Identify security vulnerabilities in a black-box environment
- Analyze API security weaknesses (BOLA, IDOR, JWT flaws, etc.)
- Demonstrate real-world exploitation techniques safely
- Map findings to OWASP standards
- Provide remediation recommendations for secure development

---

## 🧪 Scope of Testing

The following areas were included in scope:

-  Web Application
-  REST APIs
-  Authentication mechanisms & Authorization controls
-  Business Logic Components(Order Management System)
-  Community Features
-  Workshop Services
-  Vehicle service and report modules(Vehicle Management Features)
-  Denial of Service (DoS) testing

Out of scope:

- Infrastructure-level attacks (unless exposed via application)
- Social engineering
- Operating System Assessment
- Third Party Services
- Source Code Review(SAST)

---

## 🛠️ Methodology

1. Reconnaissance & Application Mapping  
2. Endpoint Enumeration  
3. Authentication Testing  
4. Authorization Testing (BOLA / IDOR)  
5. Input Validation Testing  
6. Token Analysis (JWT)  
7. Business Logic Testing  
8. Exploitation & Proof of Concept  
9. Risk Analysis & Documentation  

Aligned with:
- OWASP WSTG
- OWASP API Security Top 10

---

## 📁 Repository Structure

crapi-vapt-project/ │ ├── README.md ├── executive-summary.md ├── methodology.md ├── findings/ │   ├── idor-service-report.md │   ├── jwt-bypass.md │   └── ... ├── images/ │   ├── idor-flow.png │   └── dashboard.png └── final-report.md
