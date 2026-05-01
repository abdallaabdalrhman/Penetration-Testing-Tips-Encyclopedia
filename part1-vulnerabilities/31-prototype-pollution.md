# 31 — Prototype Pollution

**CWE-1321** | **CVSS: 5.4–9.8** | **OWASP A03:2021**

---

## What Is It?

Prototype pollution allows attackers to inject properties into JavaScript's `Object.prototype`, affecting all objects globally. Leads to XSS, RCE (server-side), logic bypass, and privilege escalation.

> **Core Problem:** Insecure object merging, cloning, or path traversal in JavaScript sets properties on `Object.prototype`.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Client-Side Prototype Pollution** | URL parameters: `?__proto__[x]=y` — pollutes `Object.prototype` in browser. |
| **Server-Side Prototype Pollution (SSPP)** | JSON body: `{'__proto__':{'admin':true}}` — pollutes server Node.js `Object.prototype`. |
| **Prototype Pollution to XSS** | Polluted property injected into DOM sink — XSS execution. |
| **Prototype Pollution to RCE** | Node.js gadget chains via polluted properties → `child_process.exec` |
| **Prototype Pollution via Constructor** | `{'constructor':{'prototype':{'isAdmin':true}}}` |

---

## Hunting Methodology

1. Test client-side: add `?__proto__[testprop]=testval` to URL — inspect `Object.prototype` in console.
2. Test via JSON body: `{"__proto__":{"polluted":true}}` in POST request.
3. Test constructor path: `{"constructor":{"prototype":{"polluted":true}}}`
4. Test server-side: `{"__proto__":{"json spaces":10}}` — if JSON response is prettified = polluted.
5. Use Burp DOM Invader — prototype pollution scanning built in.

---

## Tips & Tricks

- **#1** Client-side test: open browser console after injecting `?__proto__[x]=y` — check `({}).x`
- **#2** SSPP: inject via JSON body not just URL — many parsers use JSON objects directly.
- **#3** Constructor path: some filters block `__proto__` but not `constructor.prototype`.
- **#4** Banking Node.js microservices: SSPP can affect all requests globally if polluted early.

---

## Deadly Chains

```
Prototype pollution + XSS gadget → stored XSS via polluted property
SSPP + child_process gadget → RCE on Node.js server
Prototype pollution → isAdmin: true → privilege escalation
Client-side pollution + innerHTML gadget → DOM XSS
```
