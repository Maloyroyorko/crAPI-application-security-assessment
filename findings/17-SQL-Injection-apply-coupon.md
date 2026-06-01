# SQL Injection in Coupon Application API

## Severity
Critical

## CWE
CWE-89 - SQL Injection

## OWASP Mapping
OWASP API Top 10: API8 - Injection

## Affected Endpoint
POST /workshop/api/shop/apply_coupon

## Description
The application is vulnerable to SQL Injection in the `coupon_code` parameter of the coupon application API.

User input is directly embedded into backend SQL queries without proper sanitization or parameterized queries, allowing an attacker to manipulate SQL statements executed by the database.

This leads to unauthorized database interaction and information disclosure.

## Steps to Reproduce

1. Login as attacker account (attacker@a.com).
2. Navigate to the coupon application feature in the shop module.
3. Intercept the request using Burp Suite.
4. Send a normal request:
   {"coupon_code":"TRAC075","amount":75}
5. Modify request with a single quote payload:
   {"coupon_code":"'","amount":75}
6. Observe HTTP 500 Internal Server Error.
7. Inject SQL payload:
   {"coupon_code":"' union select version()-- -","amount":75}
8. Observe database version disclosure in response.
9. Inject enumeration payload:
   {"coupon_code":"' union select table_name FROM information_schema.tables-- -","amount":75}
10. Observe database table names returned in response.

## Proof of Concept

### Request
POST /workshop/api/shop/apply_coupon

### Request LOGS
Reference:
evidence/requests/SQL-Injection/sql_injection_coupon_apply_version.txt
evidence/requests/SQL-Injection/sql_injection_coupon_apply_table_name_print.txt
evidence/requests/SQL-Injection/sql_injection_coupon_apply_OR-Logic.txt
evidence/requests/SQL-Injection/sql_injection_coupon_apply_order-by-1.txt
evidence/requests/SQL-Injection/sql_injection_coupon_apply_order-by-2.txt


### Response
Reference:
evidence/responses/SQL-Injection/sql_injection_coupon_apply_version.txt
evidence/responses/SQL-Injection/sql_injection_coupon_apply_table_name_print.txt
evidence/responses/SQL-Injection/sql_injection_coupon_apply_OR-Logic.txt
evidence/responses/SQL-Injection/sql_injection_coupon_apply_order-by-1.txt
evidence/responses/SQL-Injection/sql_injection_coupon_apply_order-by-2.txt


### Screenshot
Reference:
evidence/screenshots/SQL-Injection/sql_injection_coupon_apply_quote.png
evidence/screenshots/SQL-Injection/sql_injection_coupon_apply_order_by_1.png
evidence/screenshots/SQL-Injection/sql_injection_coupon_apply_version.png
evidence/screenshots/SQL-Injection/sql_injection_coupon_apply_table_print.png


## Impact

- Attacker can execute arbitrary SQL queries
- Database version and schema can be disclosed
- Sensitive backend structure can be enumerated
- Potential exposure of user, order, and coupon data
- Possible escalation to full database compromise depending on privileges

## Risk Rating
Critical

## Remediation

- Use parameterized queries (prepared statements)
- Avoid dynamic SQL construction using user input
- Validate and sanitize all input fields
- Apply least privilege principle to database accounts
- Disable verbose SQL error messages in production
- Implement WAF rules for SQL injection patterns

## References
- CWE-89: https://cwe.mitre.org/data/definitions/89.html
- OWASP API Security Top 10: https://owasp.org/www-project-api-security/
