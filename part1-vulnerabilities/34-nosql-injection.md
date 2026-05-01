# 34 — NoSQL Injection

**CWE-943** | **CVSS: 7.5–9.8** | **OWASP A03:2021**

---

## What Is It?

NoSQL injection targets databases like MongoDB, CouchDB, Redis, and Cassandra using query operator abuse instead of SQL syntax, enabling auth bypass, data extraction, and in some cases RCE.

> **Core Problem:** Application passes user input directly into NoSQL query operators without sanitization.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **MongoDB Operator Injection** | Replace string with `{"$gt":""}` → matches all documents → auth bypass. |
| **JavaScript Injection ($where)** | MongoDB `$where` operator: `{"$where":"sleep(5000)"}` → DoS/RCE. |
| **Redis SSRF via RESP** | SSRF to Redis port → inject RESP protocol commands. |
| **Blind NoSQL Injection** | Boolean-based: `$regex` to extract data character by character. |

---

## Hunting Methodology

1. Identify NoSQL-backed apps: MongoDB error messages, npm/Node.js stack, `.json` endpoints.
2. Test auth bypass: replace string fields with `{"$gt":""}` or `{"$ne":"invalid"}`.
3. Test in JSON body: `{"username":{"$gt":""},"password":{"$gt":""}}`
4. Test in URL params: `?user[$gt]=&password[$gt]=`
5. Test `$where` injection: `{"$where":"sleep(5000)"}` — measure response time.
6. Blind extraction: `{"username":{"$regex":"^a"}}` — binary search through charset.
7. Mobile banking apps: many use MongoDB backend — test all auth endpoints.

---

## Tips & Tricks

- **#1** MongoDB: always test both JSON body (`{"$gt":""}`) and URL params (`?user[$gt]=`).
- **#2** Many Node.js apps use Mongoose — check if query sanitization is enabled.
- **#3** `$where` is JavaScript execution — if available, it's RCE risk in MongoDB.
- **#4** Regex-based blind: `{"$regex":"^a"}` → yes/no → extract field value character by character.

---

## Deadly Chains

```
NoSQL auth bypass → {$gt:''} on login → full account access
NoSQL $where injection → JavaScript execution → server-side RCE
SSRF to Redis → RESP protocol injection → write cron/webshell → RCE
Blind NoSQL + regex → extract admin credentials → full account takeover
```
