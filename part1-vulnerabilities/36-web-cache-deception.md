# 36 — Web Cache Deception

**CWE-444/525** | **CVSS: 5.4–8.8** | **OWASP A05:2021**

---

## What Is It?

Web Cache Deception (WCD) tricks a cache into storing authenticated/sensitive responses by appending fake static file extensions to dynamic URL paths, making personal data available to anyone with the cached URL.

> **Core Problem:** Cache and app disagree on which URLs are static — cache stores dynamic authenticated responses.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Path Confusion** | `GET /account/profile.css` → app returns profile page; cache stores it as CSS. |
| **Extension-Based WCD** | Append `/;.css`, `/.ico`, `/..%2F` to dynamic URL — cache treats as static. |
| **Delimiter Confusion** | Use `#`, `;`, `?` as delimiter: `/profile#.jpg` — cached with attacker's session. |
| **Path Normalization Difference** | Cache normalizes `/account/..%2Fprofile.css` — caches authenticated content. |

---

## Hunting Methodology

1. Identify dynamic authenticated pages: `/account/profile`, `/dashboard`, `/settings`.
2. Test basic: `GET /account/profile.css` — does app return profile data? Does cache store it?
3. Try extensions: `.js`, `.css`, `.ico`, `.jpg`, `.png` appended to authenticated URL.
4. Try delimiters: `/account/profile;.css`, `/account/profile/.css`, `/account/profile%0A.css`
5. Poison the cache: visit URL as authenticated user. Fetch same URL unauthenticated — do you get the data?
6. Confirm: check `X-Cache: HIT` in response from unauthenticated request.
7. Banking: test `/api/account/balance.js`, `/api/user/profile.css` — if cached with financial data = Critical.

---

## Tips & Tricks

- **#1** Use cache busters when testing to avoid polluting production cache.
- **#2** Confirm cache storage: fetch URL from fresh browser/IP — if you see victim data = WCD.
- **#3** Try multiple extension delimiters: `/`, `;`, `?`, `#`, `%23`, `%3F`, `%0A`.
- **#4** Banking: any financial data (balance, PAN, account number) in cached response = Critical.

---

## Deadly Chains

```
WCD → cache authenticated profile page → anyone with URL sees victim's PII
WCD + banking account page → account number/balance cached publicly
WCD + IDOR → poison multiple users' profile pages → mass data leak
WCD + admin dashboard → admin session data in public cache
```
