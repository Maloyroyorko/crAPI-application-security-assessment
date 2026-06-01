---

## 8. Remediation Recommendations

### 🛠️ Short-Term Fixes
* **Implement Object-Level Authorization (BOLA Check):** Every time a report is requested, validate that the `User_ID` stored within the active session token matches the `Owner_ID` or authorized `Mechanic_ID` tied to that specific `report_id` in the database.
* **Enforce Strict Error Responses:** If a user attempts to access a record they do not own, immediately destroy the response payload and return an `HTTP 403 Forbidden` or an `HTTP 404 Not Found` error.

### 🏗️ Long-Term Architecture Architecture
* **Centralized Authorization Middleware:** Move access control evaluations out of individual endpoint controllers and route them through a global, tested authorization framework or decorator pattern.
* **Incorporate Non-Predictable Identifiers:** Transition away from auto-incrementing integer IDs to secure, random **UUIDv4** strings or **Crypto-Based Indirect Object References** to entirely mitigate resource scraping.

---

## 9. References

* **CWE-639:** [Authorization Bypass Through User-Controlled Key](https://cwe.mitre.org/data/definitions/639.html)
* **OWASP API Security:** [Top 10 API1: Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
* **OWASP Top 10:** [A01:2021 – Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
* **OWASP Cheat Sheet:** [Authorization Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
