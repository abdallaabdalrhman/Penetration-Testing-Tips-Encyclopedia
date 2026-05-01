# 30 — JWT Attacks

**CWE-347/345** | **CVSS: 7.5–9.8** | **OWASP A07:2021**

---

## What Is It?

JWT vulnerabilities allow attackers to forge tokens, bypass authentication, and escalate privileges by exploiting weak signature verification or algorithm confusion.

> **Core Problem:** Server trusts JWT claims without properly verifying signature or algorithm.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **alg:none Attack** | Set algorithm to `'none'` — server accepts unsigned token. |
| **RS256 to HS256 Confusion** | Server expects RS256 but accepts HS256 — sign with public key as HMAC secret. |
| **Weak HMAC Secret** | Brute-force short/common HS256 secret — forge any claims. |
| **Kid Header Injection** | `kid` parameter used in SQL/file path — SQLi or path traversal. |
| **JWK Header Injection** | Embed attacker's public key in JWK header — server uses it to verify. |

---

## Hunting Methodology

1. Collect JWT from Authorization header, cookies, or response body.
2. Decode: base64url decode header and payload — identify algorithm, claims.
3. Test `alg:none`: set algorithm to `'none'`, remove signature, modify claims.
4. Test algorithm confusion: if RS256, sign with public key as HMAC HS256 secret.
5. Brute force weak secret: `jwt_tool` or `hashcat` with `rockyou.txt` on HS256 token.
6. Test `kid` injection: inject SQL or path traversal in `kid` header parameter.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **jwt_tool** | All-in-one JWT attack toolkit: alg:none, confusion, brute force, claims. |
| **hashcat** | GPU-accelerated HS256 secret brute force. |
| **jwt.io** | Decode and inspect JWT tokens visually. |
| **Burp JWT Editor extension** | Intercept and modify JWT in proxy. |

---

## Tips & Tricks

- **#1** Always decode JWT first — look at algorithm claim before choosing attack.
- **#2** `alg:none`: some libraries accept `'None'`, `'NONE'`, `'nOnE'` — test all capitalizations.
- **#3** Algorithm confusion: get server's RSA public key from `/jwks.json` or certificate.
- **#4** `kid` SQLi: `kid` header: `../../dev/null` or `' UNION SELECT 'attacker_secret'--`

---

## Deadly Chains

```
JWT alg:none → forge admin token → full admin access
JWT weak secret brute force → forge any user's token → mass ATO
Algorithm confusion + public key as secret → sign arbitrary tokens
kid SQL injection → extract HMAC secret → forge tokens
```
