# 11 — HTTP Request Smuggling

**CWE-444** | **CVSS: 7.5–9.8** | **OWASP A05:2021**

---

## What Is It?

HTTP Request Smuggling exploits discrepancies in how front-end and back-end servers parse HTTP/1.1 Transfer-Encoding and Content-Length headers.

> **Core Problem:** Front-end sees one request; back-end sees it differently. Attacker smuggles a second request.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **CL.TE** | Front-end uses Content-Length, back-end uses Transfer-Encoding. |
| **TE.CL** | Front-end uses Transfer-Encoding, back-end uses Content-Length. |
| **TE.TE** | Both use Transfer-Encoding but disagree on obfuscated headers. |
| **HTTP/2 Downgrade** | HTTP/2 front-end downgrades to HTTP/1.1 — header injection possible. |

---

## Hunting Methodology

1. Identify proxy-frontend architecture (CDN, load balancer, WAF in front of app server).
2. Test CL.TE: send request with both Content-Length and `Transfer-Encoding: chunked`.
3. Test TE.CL: obfuscate TE header: `Transfer-Encoding: xchunked`.
4. Use Burp HTTP Request Smuggler extension for automated detection.
5. Exploit: capture other users' requests by smuggling a partial request that stores cookies.
6. HTTP/2: test h2c upgrade, `:method` pseudo-header injection, header smuggling via CRLF.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp HTTP Request Smuggler** | Extension for automated CL.TE and TE.CL detection. |
| **smuggler.py** | Python tool for manual request smuggling tests. |
| **Turbo Intruder** | Timing-based smuggling confirmation. |

---

## Tips & Tricks

- **#1** Disable automatic Content-Length update in Burp before testing.
- **#2** Timing-based detection: CL.TE causes timeout if back-end waits for more data.
- **#3** Banking apps behind CDN (Akamai, Cloudflare) — test for request smuggling actively.

---

## Deadly Chains

```
Request Smuggling → Bypass WAF → Exploit SQLi/XSS blocked by WAF
Request Smuggling → Capture victim requests → steal credentials/tokens
Request Smuggling → Cache poisoning → serve malicious JS to all users
```
