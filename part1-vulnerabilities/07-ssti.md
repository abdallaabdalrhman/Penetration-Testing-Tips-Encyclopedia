# 07 — Server-Side Template Injection (SSTI)

**CWE-94** | **CVSS: 9.8 Critical** | **OWASP A03:2021**

---

## What Is It?

SSTI occurs when user input is embedded in a server-side template and executed. Can lead to full RCE. Common in apps using Jinja2, Twig, Freemarker, Velocity, Smarty.

> **Core Problem:** Template engine evaluates user input as code instead of treating it as data.

---

## Vulnerability Types

| Engine | Payload | Notes |
|--------|---------|-------|
| **Jinja2 (Python/Flask)** | `{{7*7}}` → 49 | Access config, OS via class introspection chain. |
| **Twig (PHP)** | `{{7*7}}` → 49 | `registerUndefinedFilterCallback` with exec. |
| **Freemarker (Java)** | `${7*7}` → 49 | Execute class instantiation. |
| **Velocity (Java)** | `#set` variable | `Runtime.getRuntime().exec()` |
| **Smarty (PHP)** | `{php}echo system('id');{/php}` | Direct PHP execution. |

---

## Hunting Methodology

1. Identify reflection points: name fields, email templates, error messages, search results, PDF generators.
2. Test basic: `{{7*7}}`, `${7*7}`, `#{7*7}`, `*{7*7}`, `<%=7*7%>` — check if 49 returned.
3. Identify engine from error messages and application stack.
4. Jinja2 sandbox escape: find Popen class through MRO subclasses chain.
5. Test blind SSTI: `{{sleep(5)}}` for time-based detection.
6. Use `tplmap` for automated SSTI detection and exploitation.
7. Check email templates: `'Hi {{name}}'` — what if name = `{{7*7}}`?
8. PDF generators often use template engines — try SSTI in any field included in PDF.
9. Banking: test statement generation, report templates, email notification fields.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **tplmap** | Automated SSTI detection and exploitation across all major engines. |
| **SSTImap** | Modern tplmap alternative with more engine support. |
| **Burp Suite** | Manual injection and response analysis. |

---

## Tips & Tricks

- **#1** SSTI detection polyglot: `${{<%['"}}%\` — causes errors revealing template engine.
- **#2** PDF generators are the #1 hidden SSTI surface — test every field that appears in PDF output.
- **#3** Email templates: look for `'Hi {firstname}'` in welcome emails — test `firstname={{7*7}}`.
- **#4** Blind SSTI: use time delays or DNS callbacks via `urlopen` to Burp Collaborator.
- **#5** Banking statement PDFs: customer name, address fields often injected into templates.

---

## Deadly Chains

```
SSTI → RCE → Server compromise → Database access → All user data

SSTI in email template → read /etc/passwd → internal network info

SSTI + SSRF capabilities → pivot to internal services

SSTI in PDF generator → read AWS metadata → IAM credentials → cloud takeover
```
