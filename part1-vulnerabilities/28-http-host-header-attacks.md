# 28 — HTTP Host Header Attacks

**CWE-20** | **CVSS: 5.4–8.8** | **OWASP A03:2021**

---

## What Is It?

HTTP Host header attacks exploit trusting the Host header for URL generation, routing, or access control. Leads to password reset poisoning, cache poisoning, SSRF, and auth bypass.

> **Core Problem:** Application trusts the Host header from the client to construct URLs or make routing decisions.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Password Reset Poisoning** | Inject `attacker.com` in Host → reset email contains attacker's URL. |
| **Host Header SSRF** | Host header used in server-side requests → SSRF to internal services. |
| **Cache Poisoning via Host** | Host header injected into cached responses. |
| **Routing-Based Auth Bypass** | Internal virtual host bypass: `Host: internal-admin.company.com`. |

---

## Hunting Methodology

1. Test password reset: add `X-Forwarded-Host: attacker.com` — check reset email link.
2. Also test: `X-Host`, `Forwarded: host=attacker.com`, `X-Original-Host` headers.
3. Test Host header directly: change `Host: target.com` to `Host: attacker.com`.
4. Test for internal routing: `Host: admin.internal`, `Host: localhost`, `Host: 127.0.0.1`.
5. Banking: test password reset and email confirmation flows.

---

## Tips & Tricks

- **#1** Test all Host header variants: `X-Forwarded-Host`, `X-Host`, `Forwarded`, `X-Original-URL`.
- **#2** Password reset poisoning is always High/Critical — directly enables ATO.
- **#3** Internal routing: try `Host: internal`, `Host: admin`, `Host: localhost` on public endpoints.

---

## Deadly Chains

```
Host header poisoning → password reset token sent to attacker → ATO
Host header SSRF → internal metadata API → IAM credentials
Host header + cache poisoning → poisoned password reset link cached for all users
Routing bypass → internal admin panel → no authentication
```
