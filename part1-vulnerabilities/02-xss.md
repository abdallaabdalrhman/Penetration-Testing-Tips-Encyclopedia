# 02 — Cross-Site Scripting (XSS)

**CWE-79** | **CVSS: 6.1–9.3** | **OWASP A03:2021**

---

## What Is It?

XSS allows attackers to inject malicious JavaScript into pages viewed by other users. Impacts range from session theft to full account takeover, keylogging, and phishing.

> **Core Problem:** App reflects/stores user input as HTML without encoding. Browser executes attacker JS.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Reflected XSS** | Payload in request, reflected immediately in response. Requires victim to click link. |
| **Stored XSS** | Payload saved in DB, executed for every viewer. Higher impact. |
| **DOM-Based XSS** | JavaScript reads attacker-controlled source (`location.hash`, `document.referrer`) and writes to sink (`innerHTML`). |
| **Blind XSS** | Payload fires in backend admin panel. Use XSS Hunter to detect. |
| **Self-XSS** | Only affects attacker. Escalate via CSRF to make it exploitable. |
| **mXSS (Mutation)** | Browser parses and mutates HTML, creating XSS from safe-looking input. |

---

## Hunting Methodology

1. Map all reflection points: search bars, error messages, profile fields, comments, file names.
2. Test basic: `<script>alert(1)</script>` — note if reflected, encoded, or stripped.
3. Try event handlers: `<img src=x onerror=alert(1)>`, `<svg onload=alert(1)>`
4. Check HTML context: in attribute? Try `' onmouseover='alert(1)` or breaking the attribute.
5. Check JS context: if inside JS string, try `';alert(1)//` or `</script><script>alert(1)`
6. DOM XSS: grep source code for dangerous sinks: `innerHTML`, `document.write`, `eval`, `location.href`.
7. Blind XSS: use XSS Hunter payload in every input — name, address, user-agent, support tickets.
8. WAF bypass: use encoding, polyglots, Unicode, case variation.
9. Mobile app: test WebViews — JavaScript bridges can escalate XSS to native code execution.
10. Banking: test statement export names, profile display fields, chat/support features.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **XSS Hunter** | Blind XSS detection — captures cookies, screenshots, DOM content. |
| **Dalfox** | Fast XSS scanner with context-aware payloads. |
| **XSStrike** | XSS fuzzer with WAF bypass and DOM analysis. |
| **Burp Suite** | Active scanner + manual testing via Repeater. |
| **DOM Invader (Burp)** | DOM XSS source-to-sink analysis in browser. |

---

## Tips & Tricks

- **#1** Blind XSS: inject XSS Hunter payload in EVERY text field including HTTP headers.
- **#2** Android WebView: if JS enabled + `addJavascriptInterface` used, XSS = native RCE.
- **#3** CSP bypass: check if `script-src` allows `unsafe-inline`, CDN hosts, or JSONP endpoints.
- **#4** Self-XSS + CSRF = exploitable XSS: force victim to submit XSS payload via CSRF.
- **#5** Banking PDF/export: XSS in filename can trigger JS when PDF is generated (PDF injection).
- **#6** Check `Content-Type` of JSON responses — if `text/html`, JSON XSS is possible.

---

## Deadly Chains

```
Stored XSS + Session Cookie → ATO
  └─ Steal admin cookie → full account takeover

XSS + CSRF token theft → CSRF bypass
  └─ Extract CSRF token via XSS → forge requests

XSS + OAuth redirect → Token theft
  └─ Steal authorization code from redirect_uri

Blind XSS + Admin panel → Privilege escalation
  └─ Fire in admin → steal admin session

DOM XSS + postMessage → Cross-origin data theft

XSS + Banking WebView → Native RCE on mobile (if JS bridge exposed)
```
