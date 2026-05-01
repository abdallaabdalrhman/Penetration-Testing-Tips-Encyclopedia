# 20 — Clickjacking

**CWE-1021** | **CVSS: 4.3–7.5** | **OWASP A05:2021**

---

## What Is It?

Clickjacking (UI Redress Attack) tricks users into clicking on invisible or disguised elements. Attackers overlay a transparent iframe over a legitimate page, hijacking user clicks for unauthorized actions.

> **Core Problem:** Application pages can be framed by attacker-controlled sites, enabling invisible interaction.

---

## Hunting Methodology

1. Check `X-Frame-Options` header: `DENY` or `SAMEORIGIN` should be present.
2. Check `Content-Security-Policy`: `frame-ancestors` directive.
3. Create PoC HTML with `iframe src` pointing to target — does it render?
4. Identify sensitive actions: fund transfer, email change, password change buttons.
5. Position invisible iframe precisely over decoy button using CSS.
6. Test mobile: touchjacking works similarly on mobile browsers.
7. Banking: test transfer confirmation pages, OTP confirmation buttons.

---

## Tips & Tricks

- **#1** Even with `X-Frame-Options`, check CSP `frame-ancestors` — one may be misconfigured.
- **#2** Banking: clickjacking on transfer confirmation = High minimum severity.
- **#3** Frame busting JS can be bypassed: `sandbox` iframe attribute disables JS.
- **#4** Combine with CSRF: clickjacking on CSRF-vulnerable form = high-impact chain.

---

## Deadly Chains

```
Clickjacking + Banking transfer button → invisible fund transfer click
Clickjacking + OAuth authorization → victim authorizes attacker app
Clickjacking + Admin action → unauthorized admin operation
Clickjacking + CSRF form → forced state-changing request
```
