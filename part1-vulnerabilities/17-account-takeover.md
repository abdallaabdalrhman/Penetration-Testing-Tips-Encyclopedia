# 17 — Account Takeover (ATO)

**CWE-287/620/640** | **CVSS: 8.0–10.0** | **OWASP A07:2021**

---

## What Is It?

ATO is the ultimate goal of many attack chains — full control of a victim's account. In banking, ATO leads directly to financial fraud, identity theft, and regulatory violations.

> **Core Problem:** Combination of vulnerabilities or single critical flaw allows attacker to own any account.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Credential-Based ATO** | Phishing, credential stuffing, brute force, breach data. |
| **Token-Based ATO** | Steal/forge auth tokens, session cookies, JWT, OAuth tokens. |
| **OAuth ATO** | Misuse `redirect_uri`, state, or code — steal OAuth token from callback. |
| **Password Reset ATO** | Predictable tokens, host header poisoning, token reuse. |
| **Session Fixation ATO** | Fix session before login — after victim logs in, attacker reuses. |

---

## Hunting Methodology

1. Map all authentication entry points: login, SSO, OAuth, API keys, session tokens.
2. Test password reset for all bypass techniques (poisoning, token prediction, reuse).
3. Test OAuth flows: `redirect_uri` manipulation, state parameter bypass, implicit flow token theft.
4. Test email change: does it require current password? Email confirmation? OTP?
5. Test session management: fixation, non-expiry, token entropy.
6. Test all 2FA/OTP bypass techniques.
7. Combine low-severity findings: IDOR (email leak) + Password Reset → ATO chain.
8. Banking: document full financial impact — unauthorized access + potential fraudulent transfer.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite** | Full interception and manipulation of auth flows. |
| **jwt_tool** | JWT manipulation and forgery. |
| **Evilginx2** | Real-time AiTM phishing with 2FA bypass. |

---

## Tips & Tricks

- **#1** ATO PoC must show full access — log in as victim, show PII, or perform sensitive action.
- **#2** Chain findings: IDOR leaks email → password reset poisoning → ATO = Critical report.
- **#3** Banking ATO PoC: show access to balance, statements, or initiate (but don't complete) transfer.
- **#4** OAuth: test `redirect_uri=https://attacker.com` — if allowed, code stolen = ATO.

---

## Deadly Chains

```
IDOR email leak → Password reset poisoning → ATO
XSS → Cookie theft → Session hijack → ATO
OAuth redirect_uri bypass → Code theft → Token exchange → ATO
Banking ATO → Account access → Fund transfer to attacker → Financial fraud
```
