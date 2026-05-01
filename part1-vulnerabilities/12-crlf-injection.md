# 12 — CRLF Injection

**CWE-93** | **CVSS: 5.4–7.5** | **OWASP A03:2021**

---

## What Is It?

CRLF injection (`\r\n`) allows attackers to inject HTTP headers or split HTTP responses, enabling session fixation, XSS, cache poisoning, and open redirects.

> **Core Problem:** User input containing `\r\n` is reflected in HTTP headers without sanitization.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **HTTP Header Injection** | Inject headers: `Set-Cookie`, `Location` via `\r\n` in URL parameter. |
| **HTTP Response Splitting** | Inject full second HTTP response — cache poisoning or XSS. |
| **Session Fixation via CRLF** | Inject `Set-Cookie: sessionid=attacker_value` — fix victim session. |
| **Open Redirect via CRLF** | `Location: http://evil.com%0d%0a` → redirect victim. |

---

## Hunting Methodology

1. Find URL parameters reflected in HTTP response headers (`Location`, `Set-Cookie`, etc.).
2. Test: inject `%0d%0a` (URL-encoded `\r\n`) in all parameters.
3. Also test: `%0a`, `%0d`, `%250d%250a` (double encoding), unicode newlines.
4. Check redirect parameters: `?redirect=`, `?next=`, `?url=` with CRLF injection.
5. Test for session fixation: inject `Set-Cookie: session=attacker` → visit app → check if session used.
6. Test response splitting: inject full HTTP response after `\r\n\r\n`.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite Repeater** | Manual CRLF injection testing via encoded newlines. |
| **CRLFuzz** | Automated CRLF injection fuzzer. |
| **Dalfox** | Detects CRLF alongside XSS patterns. |

---

## Tips & Tricks

- **#1** Test all encoding variants: `%0d%0a`, `%0a`, `%0d`, `%250d%250a`.
- **#2** Look for redirect parameters — they're most commonly vulnerable.
- **#3** CRLF + XSS: inject `Content-Type: text/html\r\n\r\n<script>alert(1)</script>`
- **#4** Banking: test callback URLs and redirect parameters in payment flows.

---

## Deadly Chains

```
CRLF → Set-Cookie injection → Session fixation → ATO after victim logs in
CRLF → Response splitting → Cache poisoning → XSS for all users
CRLF → Open redirect → Phishing page with legitimate URL prefix
```
