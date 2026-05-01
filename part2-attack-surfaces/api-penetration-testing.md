# API Penetration Testing

**OWASP API Top 10** | **REST • GraphQL • gRPC • SOAP**

---

## OWASP API Top 10

| # | Vulnerability | Key Test |
|---|--------------|----------|
| **API1** | Broken Object Level Authorization | Test every object ID with another user's token. |
| **API2** | Broken Authentication | Weak tokens, no expiry, API key in URL, JWT none alg. |
| **API3** | Broken Object Property Level Auth | Mass assignment — send extra fields (`role`, `isAdmin`). |
| **API4** | Unrestricted Resource Consumption | No rate limits, large payload DoS, batch request abuse. |
| **API5** | Broken Function Level Authorization | Admin-only endpoints accessible to regular users. |
| **API6** | Unrestricted Access to Sensitive Business Flows | Coupon abuse, price manipulation. |
| **API7** | Server-Side Request Forgery | Webhook/callback URLs processed server-side. |
| **API8** | Security Misconfiguration | Debug endpoints, verbose errors, CORS *, default credentials. |
| **API9** | Improper Inventory Management | Shadow APIs, deprecated `/v1` vs `/v2`. |
| **API10** | Unsafe Consumption of APIs | Third-party API responses not validated. |

---

## Essential API Testing Tools

| Tool | Purpose |
|------|---------|
| **Postman / Insomnia** | API request builder — import Swagger, manage collections. |
| **Burp Suite** | Intercept, modify, replay API requests. |
| **ffuf** | Fuzz API endpoints, parameters, and values. |
| **Nuclei** | API security templates: auth bypass, CORS, info disclosure. |
| **jwt_tool** | JWT manipulation, alg:none, RS256→HS256. |
| **Arjun** | Discover hidden API parameters via wordlists. |
| **kiterunner** | API endpoint discovery and fuzzing at scale. |

---

## API Testing Checklist

### Authentication
- [ ] JWT algorithm confusion (`alg:none`, RS256→HS256)
- [ ] Weak HMAC secrets (brute forceable with hashcat)
- [ ] Token expiry not enforced
- [ ] Token not invalidated on logout
- [ ] API keys exposed in URL parameters

### Authorization
- [ ] IDOR on all object IDs (sequential and UUID)
- [ ] Horizontal privilege escalation
- [ ] Vertical privilege escalation (user → admin)
- [ ] HTTP method bypass (GET blocked but POST works)

### Input Validation
- [ ] SQLi in all query parameters
- [ ] NoSQL injection in JSON body
- [ ] XXE in XML-consuming endpoints
- [ ] Mass assignment (`isAdmin: true`, `role: admin`)

### Rate Limiting
- [ ] Login endpoint rate limiting
- [ ] OTP endpoint rate limiting
- [ ] IP rotation bypass via `X-Forwarded-For`
