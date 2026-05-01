# 21 — DOM-Based Vulnerabilities

**CWE-79/601/116** | **CVSS: 4.3–9.3** | **OWASP A03:2021**

---

## What Is It?

DOM-based vulnerabilities occur when JavaScript reads attacker-controlled data from sources and passes it to dangerous sinks without sanitization, entirely in the browser.

> **Core Problem:** Server-side code is clean; vulnerability exists in client-side JavaScript logic.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **DOM XSS** | Source: `location.hash` → Sink: `innerHTML` → script execution. |
| **DOM-based Open Redirect** | Source: `location.search` → Sink: `location.href = userInput` → redirect. |
| **DOM-based Cookie Manipulation** | Attacker writes to `document.cookie` via DOM source. |
| **postMessage Vulnerabilities** | Insecure message handler accepts data from any origin. |
| **DOM Clobbering** | HTML injection overwrites global JS variables via named elements. |

---

## Hunting Methodology

1. Identify all sources: `location.search`, `location.hash`, `location.href`, `document.referrer`, `postMessage`.
2. Identify all dangerous sinks: `innerHTML`, `document.write`, `eval`, `setTimeout`, `location.href`.
3. Use Burp DOM Invader to trace source-to-sink flows automatically.
4. Grep JS files for dangerous sink patterns: `innerHTML`, `document.write`, `eval()`.
5. Test `postMessage` handlers: create a page that sends arbitrary messages to target window.
6. Test DOM-based open redirect: `?redirect=javascript:alert(1)` or `?url=//attacker.com`.

---

## Tips & Tricks

- **#1** DOM vulnerabilities are invisible to server-side scanners — manual JS review is essential.
- **#2** `postMessage`: check if origin validation is missing — `window.addEventListener('message', ...)` without origin check.
- **#3** Fragment (`#`) is never sent to server — only DOM-based attacks can exploit it.
- **#4** Banking SPAs (React/Angular/Vue): heavy client-side logic = more DOM attack surface.

---

## Deadly Chains

```
DOM XSS + CORS → read sensitive API responses cross-origin
DOM open redirect → phishing with legitimate domain prefix
postMessage without origin check → cross-origin data theft
DOM Clobbering → prototype pollution → XSS
```
