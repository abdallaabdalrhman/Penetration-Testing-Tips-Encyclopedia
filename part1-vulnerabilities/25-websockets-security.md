# 25 — WebSockets Security

**CWE-345/346** | **CVSS: 4.3–9.0** | **OWASP A01:2021**

---

## What Is It?

WebSocket vulnerabilities arise from missing authentication, lack of origin validation, and insecure message handling in real-time communication channels.

> **Core Problem:** WebSocket connections bypass many security controls that apply to HTTP.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **CSWSH** | Like CSRF for WebSockets — attacker connects to victim's WebSocket with their session. |
| **WebSocket Message Injection** | Inject malicious payloads — SQLi, XSS, command injection. |
| **Insecure Authentication** | No token/auth on WebSocket upgrade — any user can connect. |
| **Missing Origin Validation** | Server accepts WebSocket connections from any origin. |
| **Cleartext WebSocket** | `ws://` instead of `wss://` — traffic interception possible. |

---

## Hunting Methodology

1. Open Burp Suite → Proxy → WebSockets history — capture all WS messages.
2. Test CSWSH: does the WS upgrade request include CSRF token? Is Origin validated?
3. Build CSWSH PoC: HTML page that opens WS connection using victim cookies.
4. Test injections in WS message payloads: SQLi, XSS, command injection.
5. Test message tampering: modify numeric values, IDs, action types in WS messages.
6. Check `ws://` vs `wss://` — cleartext WebSocket = traffic interception risk.

---

## Tips & Tricks

- **#1** Burp Repeater works with WebSockets — right-click WS message → send to Repeater.
- **#2** CSWSH: if no CSRF token and no Origin check on WS upgrade = vulnerable.
- **#3** Banking real-time balance feeds: CSWSH could expose live transaction data.

---

## Deadly Chains

```
CSWSH → read victim's real-time banking transactions cross-origin
WebSocket injection → SQLi → database exfiltration
WebSocket auth bypass → access other users' message channels → data theft
WS message tampering → business logic bypass → unauthorized actions
```
