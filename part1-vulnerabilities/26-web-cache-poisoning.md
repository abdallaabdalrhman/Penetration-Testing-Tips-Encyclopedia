# 26 — Web Cache Poisoning

**CWE-444** | **CVSS: 5.4–9.0** | **OWASP A05:2021**

---

## What Is It?

Web cache poisoning allows attackers to store malicious content in a CDN or reverse proxy cache, serving it to all subsequent users who request the same resource.

> **Core Problem:** Cache uses unkeyed inputs (headers, cookies) in responses — attacker controls cached content.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Header-Based Cache Poisoning** | Inject `X-Forwarded-Host` to poison cached redirect or resource URL. |
| **Parameter-Based Cache Poisoning** | Fat GET: add body to GET request — injected into cache. |
| **Web Cache Deception** | Force caching of dynamic authenticated content via fake extension. |
| **Response Splitting via Cache** | CRLF injection + cache stores poisoned response for all users. |
| **Unkeyed Cookie Poisoning** | Cookie value reflected in response but not included in cache key. |

---

## Hunting Methodology

1. Identify caching: check `Cf-Cache-Status`, `X-Cache`, `Age`, `Via` response headers.
2. Find unkeyed inputs: add arbitrary headers and check if they're reflected in cached response.
3. Test `X-Forwarded-Host` injection: add header with `attacker.com` — does response reflect it?
4. Check if reflected header value is stored in cache — fetch from different IP to confirm.
5. Use Param Miner to discover unkeyed parameters automatically.
6. Banking: cache poisoning on JS files = XSS for all users visiting the site.

---

## Tips & Tricks

- **#1** Always use cache busters (`?cb=random`) during testing to avoid poisoning live cache.
- **#2** Confirm poisoning: fetch from different IP/browser — if you see injected content, it's cached.
- **#3** Banking CDN resources: poisoning JS files = stored XSS affecting all users.

---

## Deadly Chains

```
Cache poisoning + XSS payload → stored XSS for all users via CDN
Cache poisoning + redirect → phish all users via cached 302 with attacker.com
X-Forwarded-Host injection → poisoned password reset link in cached email template
Cache poisoning + banking JS → keylogger served to all banking users
```
