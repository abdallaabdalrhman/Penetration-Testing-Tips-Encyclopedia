# 29 — OAuth Authentication Attacks

**CWE-601/287** | **CVSS: 5.4–9.8** | **OWASP A07:2021**

---

## What Is It?

OAuth implementation flaws allow attackers to steal authorization codes, bypass authentication, perform ATO, and escalate privileges through misused flows.

> **Core Problem:** OAuth is complex — misconfigured `redirect_uri`, missing state, or implicit flow misuse leads to token theft.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **redirect_uri Manipulation** | Change `redirect_uri` to `attacker.com` — authorization code sent there. |
| **State Parameter Bypass** | No CSRF protection on OAuth callback — CSRF login attack. |
| **Implicit Flow Token Theft** | Token in URL fragment — Referer header or browser history leaks it. |
| **Account Linking Bypass** | Link victim's social account to attacker's app account — ATO. |
| **OAuth to ATO via Reused Email** | App auto-merges accounts by email — OAuth email not verified. |

---

## Hunting Methodology

1. Map the OAuth flow: identify authorization endpoint, token endpoint, `redirect_uri`.
2. Test `redirect_uri`: add extra path, inject open redirect, try `attacker.com` subdomain.
3. Test state parameter: is it present? Is it validated? Replay same state value.
4. Test implicit flow: does the `access_token` appear in URL? Can it leak via Referer?
5. Test code reuse: replay authorization code — should only be accepted once.
6. Banking: OAuth for Open Banking API — test all above vectors.

---

## Tips & Tricks

- **#1** `redirect_uri`: try path traversal, subdomain, encoded slashes to bypass whitelist.
- **#2** Implicit flow is deprecated but still found — tokens in URL = high risk.
- **#3** Banking Open Banking APIs often use OAuth 2.0 — all vectors apply.

---

## Deadly Chains

```
redirect_uri bypass → auth code theft → token exchange → full ATO
Missing state + CSRF → force victim to link attacker's OAuth account → ATO
Implicit flow token in URL → Referer leak to third-party analytics → token theft
OAuth email not verified → account merge → victim account takeover
```
