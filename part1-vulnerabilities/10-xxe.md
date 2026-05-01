# 10 — XML External Entity (XXE)

**CWE-611** | **CVSS: 7.5–9.8** | **OWASP A05:2021**

---

## What Is It?

XXE attacks exploit XML parsers that process external entity references. Can lead to file disclosure, SSRF, RCE, and DoS via billion laughs attack.

> **Core Problem:** XML parser resolves external entities defined by attacker, allowing file read and SSRF.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Classic XXE — File Read** | Entity triggers `file:///etc/passwd` read — contents in response. |
| **Blind XXE via OOB** | Entity triggers HTTP/DNS callback to Collaborator — no in-band output. |
| **XXE via SVG** | SVG upload with embedded DOCTYPE — server parses entities. |
| **XXE via XLSX/DOCX** | Unzip Office files — modify `xl/workbook.xml` — rezip and upload. |
| **XXE → SSRF** | Use `SYSTEM 'http://169.254.169.254/'` to hit cloud metadata. |

---

## Hunting Methodology

1. Find XML input: SOAP endpoints, file upload (SVG, XML, XLSX, DOCX), `Content-Type: text/xml`.
2. Test basic: inject DOCTYPE with `SYSTEM file:///etc/passwd` — check response.
3. Test blind: use Burp Collaborator URL in SYSTEM — look for DNS/HTTP hit.
4. Try XInclude if DOCTYPE not allowed: `xi:include href='file:///etc/passwd'`
5. Upload SVG with XXE DOCTYPE entity referencing external file.
6. XLSX XXE: unzip → modify `sharedStrings.xml` or `workbook.xml` → re-zip → upload.
7. Parameter entities for blind XXE: DTD-based exfiltration via out-of-band channel.
8. Banking: test XML-based API calls, SOAP services, financial data import features.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite** | Inject XXE payloads in XML requests via Repeater. |
| **XXEinjector** | Automated XXE exploitation tool. |
| **Burp Collaborator** | OOB XXE detection. |

---

## Tips & Tricks

- **#1** Always check file uploads — SVG, XLSX, DOCX, XML are XXE attack surfaces.
- **#2** SOAP APIs in banking are often vulnerable — they use XML by default.
- **#3** XInclude bypass: when DOCTYPE is blocked, try `xi:include` namespace.
- **#4** XXE → SSRF: use `SYSTEM 'http://169.254.169.254/'` to hit cloud metadata.
- **#5** Exfil binary files via PHP base64 wrapper: `php://filter/convert.base64-encode/resource=`

---

## Deadly Chains

```
XXE → file:///etc/passwd → username enumeration → SSH brute force
XXE → SSRF → AWS metadata → IAM credentials → cloud takeover
XXE → /var/www/html/config.php → DB credentials → full DB access
XXE in XLSX upload → banking system → internal network scan
```
