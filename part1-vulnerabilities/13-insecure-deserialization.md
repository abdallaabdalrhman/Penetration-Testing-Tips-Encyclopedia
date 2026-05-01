# 13 — Insecure Deserialization

**CWE-502** | **CVSS: 8.1–9.8** | **OWASP A08:2021**

---

## What Is It?

Insecure deserialization occurs when attacker-controlled data is deserialized, allowing object injection, RCE, privilege escalation, and data tampering.

> **Core Problem:** Application deserializes untrusted data without validation.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Java Deserialization** | `rO0AB` base64 magic bytes. Exploitable with ysoserial gadget chains. |
| **PHP Object Injection** | `__wakeup()`, `__destruct()` called on `unserialize()` — property injection leads to RCE. |
| **Python Pickle** | `pickle.loads()` executes arbitrary code — `os.system()` in `__reduce__`. |
| **Ruby Marshal** | `Marshal.load` on user input — code execution. |
| **Node.js** | `node-serialize`, `YAML.load` with user input. |

---

## Hunting Methodology

1. Find serialized data: cookies (base64 blobs starting `rO0AB`), POST bodies, file uploads, ViewState.
2. Identify language/framework from HTTP headers, error messages, file extensions.
3. Java: base64 `rO0AB` prefix → use ysoserial with gadget chains (CommonsCollections, Spring, etc.).
4. PHP: `O:8:'UserData':2:{...}` — inject magic methods via property manipulation.
5. Python pickle: craft exploit with `__reduce__` returning `(os.system, ('id',))`
6. Test blind: inject serialized object that triggers DNS callback (Burp Collaborator).
7. Check ASP.NET ViewState — if MAC validation disabled, arbitrary deserialization possible.
8. Banking: check session tokens, persistent cookies, API request bodies for serialized objects.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **ysoserial** | Java deserialization payload generator with gadget chains. |
| **PHPGGC** | PHP gadget chain generator for `unserialize()` exploits. |
| **Burp Deserialization Scanner** | Detect Java deserialization in HTTP traffic. |
| **Pickle exploit scripts** | Custom Python scripts for pickle deserialization attacks. |

---

## Tips & Tricks

- **#1** Java magic bytes: `AC ED` (hex) / `rO0AB` (base64) — always grep cookies and body for these.
- **#2** ViewState: decode from base64, check if MAC validation enforced — if not, game over.
- **#3** PHP: look for `unserialize()` in source code — trace all user-controlled paths to it.
- **#4** Python: pickle is common in ML/data science APIs — often overlooked.

---

## Deadly Chains

```
Java deserialization → RCE → internal network pivot → DB access → all user data
PHP object injection → file write → webshell → persistent access
ViewState deserialization → RCE on ASP.NET → Windows domain pivot
```
