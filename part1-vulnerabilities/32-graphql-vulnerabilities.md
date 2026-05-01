# 32 — GraphQL API Vulnerabilities

**CWE-284/400** | **CVSS: 4.3–9.8** | **OWASP API4:2023**

---

## What Is It?

GraphQL APIs introduce unique attack surfaces including introspection abuse, IDOR via queries, batching for brute force bypass, and excessive data exposure through overpermissive resolvers.

> **Core Problem:** Single endpoint with complex query language creates difficult-to-secure authorization.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Introspection Enabled** | Dump full schema: types, fields, mutations — map entire API. |
| **Batching / Alias Attacks** | Send 1000 login mutations in one request — bypass rate limiting. |
| **IDOR via Queries** | `query user(id:1234)` with victim's ID using your auth token. |
| **Excessive Data Exposure** | Query all fields — API returns sensitive data not shown in UI. |
| **Mutation Abuse** | Invoke admin mutations without authorization check. |
| **Nested Query DoS** | Deeply nested queries cause exponential resource consumption. |

---

## Hunting Methodology

1. Test introspection: `{__schema{types{name,fields{name}}}}` — enumerate all types and fields.
2. If introspection blocked, try: `{__type(name:"User"){fields{name}}}` or use InQL tool.
3. Test IDOR: `query user(id:VICTIM_ID)`, `transaction(id:1)`, `account(id:2)` with your token.
4. Test batching: `[{"query":"mutation{login(user:'x',pass:'a')}"},... x1000]`
5. Use aliases to bypass rate limiting: `{a:login(u:x) b:login(u:y) c:login(u:z)}`
6. Banking: test account query, transaction query, transfer mutation with another user's ID.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **InQL (Burp)** | GraphQL schema extraction and security testing. |
| **GraphQL Voyager** | Visualize schema after introspection. |
| **CrackQL** | GraphQL brute force and enumeration via batching. |
| **clairvoyance** | Recover schema even when introspection is disabled. |

---

## Tips & Tricks

- **#1** Introspection disabled? Use `clairvoyance` or field suggestion errors to reconstruct schema.
- **#2** Batching: single HTTP request with array of operations = rate limit bypass.
- **#3** Test deprecated fields — they often lack security controls added to newer fields.
- **#4** Banking GraphQL: `query{me{accountNumber, balance, transactions{amount}}}` — check field exposure.

---

## Deadly Chains

```
GraphQL introspection → discover admin mutations → test without auth
Batching + login mutation → brute force bypass → account compromise
IDOR via query → read all users' financial data
Excessive field exposure → PAN/CVV returned in query response → PCI violation
```
