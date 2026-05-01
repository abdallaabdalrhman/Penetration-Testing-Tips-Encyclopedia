# 27 — Information Disclosure

**CWE-200/209/497** | **CVSS: 3.7–7.5** | **OWASP A02:2021**

---

## What Is It?

Information disclosure vulnerabilities expose sensitive data that helps attackers compromise the application, including source code, credentials, internal paths, stack traces, and API keys.

> **Core Problem:** Application reveals more information than necessary in errors, responses, or files.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Verbose Error Messages** | Stack traces revealing file paths, DB schema, framework versions. |
| **Debug Endpoints Exposed** | `/debug`, `/phpinfo.php`, `/server-info`, `/actuator/env` left in production. |
| **Source Code Disclosure** | `.git/` directory exposed — full source code retrieval. |
| **API Key / Secret Leakage** | Credentials in JS files, API responses, git commits, error messages. |
| **Directory Listing** | Apache/Nginx directory browsing enabled — lists all files. |
| **Backup File Exposure** | `config.php.bak`, `.env.backup`, `db.sql` left accessible. |

---

## Hunting Methodology

1. Test error messages: send malformed requests, invalid IDs, SQL characters — check stack traces.
2. Check `/.git/config` — if accessible, run git-dumper to download full repo.
3. Check `/.env`, `/config.php`, `/web.config`, `/application.properties` for exposed secrets.
4. Search JS files for API keys: grep for `api_key`, `secret`, `token`, `password`.
5. Test debug endpoints: `/actuator`, `/api-docs`, `/swagger-ui`, `/phpinfo.php`, `/server-status`.
6. Banking: check if account numbers, full PAN, or CVV appear in any API response.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **git-dumper** | Download exposed `.git` directory and reconstruct source. |
| **SecretFinder** | Extract API keys and secrets from JavaScript files. |
| **truffleHog** | Scan git history for leaked secrets. |
| **Nuclei** | Info disclosure templates: `.git`, `.env`, backup files, debug endpoints. |

---

## Tips & Tricks

- **#1** `.git` exposed: `git-dumper target.com/.git ./repo` — then `git log` for commit history.
- **#2** Check all JS files — minified JS often still contains hardcoded keys.
- **#3** Backup files: fuzz for `.bak`, `.old`, `.backup`, `~`, `.swp` extensions on known files.
- **#4** Banking: full PAN in API response = PCI DSS violation → Critical finding regardless of CVSS.

---

## Deadly Chains

```
.git exposure → source code → hardcoded credentials → full compromise
API key in JS → cloud account access → S3 buckets → all user data
Stack trace → internal IP → internal service attack
DB credentials in error → direct database access → all customer records
```
