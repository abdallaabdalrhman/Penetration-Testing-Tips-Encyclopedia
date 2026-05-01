# 22 — Cross-Origin Resource Sharing (CORS)

**CWE-346** | **CVSS: 4.3–8.8** | **OWASP A05:2021**

---

## What Is It?

CORS misconfigurations allow attacker-controlled websites to make cross-origin requests and read sensitive responses. Critical when combined with authentication cookies.

> **Core Problem:** Server reflects arbitrary Origin or trusts null origin, allowing cross-origin data theft.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Reflected Origin** | Server reflects any Origin in ACAO header. |
| **Null Origin Trusted** | Server allows `null` origin — sandboxed iframes can exploit this. |
| **Regex Bypass** | Pattern match: allowed `bank.com` but attacker registers `evilbank.com`. |
| **Trusted Subdomain + XSS** | XSS on trusted subdomain used to bypass CORS restriction. |
| **CORS + Credentials** | `Access-Control-Allow-Credentials: true` + wildcard-like config = data theft. |

---

## Hunting Methodology

1. Add `Origin: https://attacker.com` header to sensitive API requests — check ACAO response.
2. Test null origin: `Origin: null` — does server respond with `ACAO: null`?
3. Test subdomain variations: `Origin: attacker.target.com`, `Origin: targetevilattacker.com`.
4. Check if `ACAO: *` is set on authenticated endpoints — should never be combined with credentials.
5. Build PoC: `fetch()` cross-origin with `credentials: 'include'` — can you read the response?
6. Banking: test `/api/account/balance`, `/api/transactions` — must not allow cross-origin reads.

---

## Tips & Tricks

- **#1** `ACAO: *` is safe only for public APIs with no auth — never with cookies or sensitive data.
- **#2** Regex match: `target.com` may allow `evil-target.com` — always test prefix/suffix variations.
- **#3** Banking: any CORS misconfiguration on financial APIs = High or Critical severity.
- **#4** CORS doesn't block requests — it blocks cross-origin reads. Server still processes the request.

---

## Deadly Chains

```
CORS + Authenticated endpoint → read account balance/PII cross-origin
CORS + XSS on subdomain → bypass CORS restriction via trusted origin
CORS + CSRF token → read token cross-origin → forge authenticated requests
Reflected origin + credentials → full cross-origin API access as victim
```
