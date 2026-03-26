API Security Risk Analysis Report
### Cybersecurity Task 3 — Future Interns (2026)

> **Author:** Omoding innocent— Cybersecurity Intern  
> **Date:** 26 March 2026  
> **Methodology:** OWASP API Security Top 10 (2023)  
> **Classification:** Educational — For Training Purposes Only

---

## 📋 Table of Contents

- [About the Project](#about-the-project)
- [Repository Structure](#repository-structure)
- [Tools Used](#tools-used)
- [Scope & Ethics](#scope--ethics)
- [Methodology](#methodology)
- [APIs Tested](#apis-tested)
- [Key Findings Summary](#key-findings-summary)
- [Risk Classification](#risk-classification)
- [Recommendations](#recommendations)

---

## 📌 About the Project

This repository contains the deliverables for **Cybersecurity Task 3** focused on **API Security Risk Analysis**.

The goal is to work like a real **AppSec / SOC Analyst**:
- Analyze real public demo APIs using Postman and DevTools
- Identify API security risks mapped to the OWASP API Security Top 10
- Classify findings by severity (High / Medium / Low)
- Assess business impact for each risk
- Produce a professional security report usable as a real agency deliverable

> ⚠️ All testing was **read-only** on **public demo APIs** specifically designed for testing. No exploitation or private APIs were involved.

---

## 📁 Repository Structure

```
📦 api-security-risk-analysis-task3/
├── 📄 README.md                          
├── 📄 API_Security_Risk_Analysis_Report_Task3.docx   ← Full report (Word)
└── 📁 evidence/
    ├── 🖼️ postman_users.png           ← GET /users — PII exposure proof
    ├── 🖼️ postman_posts.png           ← GET /posts — bulk unauthenticated access
    └── 🖼️ jsonplaceholder_site.png    ← API homepage confirming public access
```

---

## 🛠️ Tools Used

| Tool | Purpose | Link |
|------|---------|-------|
| **Postman** | Send API requests, inspect responses, view JSON data | [postman.com](https://www.postman.com) |
| **Browser DevTools** | Inspect HTTP headers, response codes, CORS config | Built-in (F12) |
| **OWASP API Security Top 10** | Framework for classifying API vulnerabilities | [owasp.org](https://owasp.org/www-project-api-security/) |
| **JSONPlaceholder** | Demo API used for testing — no auth, simulated user data | [jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com) |
| **ReqRes** | Demo API with login/token endpoint for auth testing | [reqres.in](https://reqres.in) |

---

## ⚖️ Scope & Ethics

### ✅ Allowed (What was done)
- GET requests to public/demo endpoints only
- Response header and payload inspection
- Documentation-based vulnerability analysis
- Read-only testing with no data modification

### ❌ Not Allowed (What was NOT done)
- Exploitation or authentication bypass attempts
- Request flooding or DoS testing
- Testing against private or production APIs
- Any form of automated scanning or brute force

**All testing was conducted ethically and legally.**

---

## 🔍 Methodology

The analysis followed a structured **5-step approach** aligned with OWASP API Security Top 10 (2023):

```
Step 1 — RECONNAISSANCE
        ↓
   Document all endpoints and HTTP methods from API documentation

Step 2 — AUTHENTICATION TESTING
        ↓
   Verify if endpoints respond without authentication tokens

Step 3 — RESPONSE INSPECTION
        ↓
   Analyze JSON payloads for PII and sensitive data fields

Step 4 — HEADER ANALYSIS
        ↓
   Check for: CSP, HSTS, X-Content-Type-Options, Rate-Limit headers

Step 5 — RISK CLASSIFICATION
        ↓
   Assign OWASP category + severity (HIGH / MEDIUM / LOW) to each finding
```

---

## 🌐 APIs Tested

### API 1 — JSONPlaceholder
| Field | Value |
|-------|-------|
| Base URL | `https://jsonplaceholder.typicode.com` |
| Type | Free public REST API for testing |
| Endpoints tested | `/users`, `/posts`, `/comments`, `/todos`, `/photos` |
| Authentication | **None required** |
| Data type | Simulated users with names, emails, addresses, geo-coords |

### API 2 — ReqRes
| Field | Value |
|-------|-------|
| Base URL | `https://reqres.in` |
| Type | Hosted REST API for frontend testing |
| Endpoints tested | `/api/users`, `/api/login`, `/api/register` |
| Authentication | Partial (login returns token) |
| Data type | Simulated user accounts with email and avatar |

---

## 🔎 Key Findings Summary

| # | Security Risk | Severity | OWASP ID | Endpoint |
|---|--------------|----------|----------|----------|
| 1 | Unauthenticated Access to All Endpoints | 🔴 HIGH | API1:2023 | `GET /users` |
| 2 | Excessive PII Exposure in Responses | 🔴 HIGH | API3:2023 | `GET /users/:id` |
| 3 | No Rate Limiting on Any Endpoint | 🟠 MEDIUM | API4:2023 | All endpoints |
| 4 | Missing HTTP Security Headers | 🟠 MEDIUM | API7:2023 | All endpoints |
| 5 | Predictable Sequential Resource IDs | 🟠 MEDIUM | API1:2023 | `GET /users/:id` |
| 6 | Auth Tokens Without Documented Expiry | 🟡 LOW | API2:2023 | `POST /api/login` |

---

## 🚨 Risk Classification

```
┌──────────────────────────────────────────────────────────────┐
│  RISK-01  ⚠ HIGH    — Unauthenticated Access                │
│  RISK-02  ⚠ HIGH    — PII Exposure (address, geo, email)    │
│  RISK-03  ⚡ MEDIUM  — No Rate Limiting                      │
│  RISK-04  ⚡ MEDIUM  — Missing Security Headers              │
│  RISK-05  ⚡ MEDIUM  — Sequential / Predictable IDs (IDOR)   │
│  RISK-06  ℹ LOW     — Token Expiry Not Documented           │
└──────────────────────────────────────────────────────────────┘
```

### Evidence — Postman Screenshots

**GET /users → HTTP 200, NO authentication, full PII returned:**

The response includes for each user: `name`, `username`, `email`, `phone`, `website`, `address` (street, suite, city, zip), and `geo` (lat/lng coordinates) — all without any token.

**GET /posts → HTTP 200, 100 records, sequential IDs:**

All 100 posts returned with `userId` and `id` as sequential integers — confirming predictable enumeration vulnerability.

---

## ✅ Recommendations

### Immediate (HIGH)
- Implement **JWT / OAuth 2.0** authentication on all non-public endpoints
- Apply **response field filtering** (return only required fields — never PII by default)
- Enforce **HTTPS-only** + add `Strict-Transport-Security` header

### Short-Term / 30 days (MEDIUM)
- Add **API gateway rate limiting** with `HTTP 429` + `Retry-After` header
- Add security headers: `CSP`, `X-Content-Type-Options`, `X-Frame-Options`
- Replace sequential IDs with **UUIDs** to prevent IDOR / enumeration

### Long-Term / 90 days (LOW)
- Document **token expiry** and implement refresh token rotation
- Establish API security review process for every new endpoint
- Implement **API access logging** and anomaly detection

---

## 📚 References

> Used for **learning and study only**.

- [OWASP API Security Top 10 (2023)](https://owasp.org/www-project-api-security/)
- [OWASP API Security GitHub](https://github.com/OWASP/API-Security)
- [API Security Checklist](https://github.com/shieldfy/API-Security-Checklist)
- [JSONPlaceholder](https://jsonplaceholder.typicode.com)
- [ReqRes](https://reqres.in)
- [Public APIs Collection](https://github.com/public-apis/public-apis)

---

## 🏷️ Tags

`api-security` `owasp` `owasp-top-10` `postman` `rest-api` `cybersecurity` `appsec` `soc-analyst` `jwt` `rate-limiting` `future-interns` `internship` `2026`

---

*This project was completed as part of the Future Interns Cybersecurity Internship Program (2026).*  
*All analysis is read-only, ethical, and for educational purposes only. No private or production systems were tested.*

