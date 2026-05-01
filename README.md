# 🔐 Penetration Testing Tips Encyclopedia

> Compiled by **Abdalla Abdelrhman** — Security Engineer  
> Web • Mobile • API • Cloud Security (AWS, Azure, GCP) • AD • AI/LLM  
> Internal and External Network Infrastructure • Adversary Emulation / Red Teaming

---

## 📖 Overview

A comprehensive penetration testing reference covering **37 vulnerability classes**, **5 full methodologies**, and domain-specific attack surfaces for Web, Mobile (Android & iOS), API, and Source Code Review — with a **Banking Application focus**.

---

## 📁 Repository Structure

```
pentest-encyclopedia/
├── part1-vulnerabilities/          # 37 Vulnerability Deep Dives
│   ├── 01-sql-injection.md
│   ├── 02-xss.md
│   ├── 03-broken-access-control.md
│   ├── 04-bypass-2fa-mfa.md
│   ├── 05-bypass-otp.md
│   ├── 06-login-reset-registration.md
│   ├── 07-ssti.md
│   ├── 08-ssrf.md
│   ├── 09-csrf.md
│   ├── 10-xxe.md
│   ├── 11-http-request-smuggling.md
│   ├── 12-crlf-injection.md
│   ├── 13-insecure-deserialization.md
│   ├── 14-ldap-injection.md
│   ├── 15-unrestricted-file-upload.md
│   ├── 16-rate-limiting-bypass.md
│   ├── 17-account-takeover.md
│   ├── 18-rce.md
│   ├── 19-business-logic.md
│   ├── 20-clickjacking.md
│   ├── 21-dom-vulnerabilities.md
│   ├── 22-cors.md
│   ├── 23-os-command-injection.md
│   ├── 24-path-traversal.md
│   ├── 25-websockets-security.md
│   ├── 26-web-cache-poisoning.md
│   ├── 27-information-disclosure.md
│   ├── 28-http-host-header-attacks.md
│   ├── 29-oauth-attacks.md
│   ├── 30-jwt-attacks.md
│   ├── 31-prototype-pollution.md
│   ├── 32-graphql-vulnerabilities.md
│   ├── 33-race-conditions.md
│   ├── 34-nosql-injection.md
│   ├── 35-web-llm-attacks.md
│   ├── 36-web-cache-deception.md
│   └── 37-essential-skills-recon.md
├── part2-attack-surfaces/          # Domain-Specific Attack Surfaces
│   ├── banking-application.md
│   ├── api-penetration-testing.md
│   ├── mobile-penetration-testing.md
│   └── source-code-review.md
├── part3-methodologies/            # Full Methodologies
│   ├── web-pentest-internal-external.md
│   ├── web-pentest-gray-black-box.md
│   ├── source-code-review-methodology.md
│   ├── mobile-static-dynamic-analysis.md
│   └── banking-application-methodology.md
└── README.md
```

---

## 📚 Part I — Vulnerability Deep Dives

| # | Vulnerability | CWE | CVSS | OWASP |
|---|--------------|-----|------|-------|
| 01 | [SQL Injection](part1-vulnerabilities/01-sql-injection.md) | CWE-89 | 9.8 Critical | A03:2021 |
| 02 | [Cross-Site Scripting (XSS)](part1-vulnerabilities/02-xss.md) | CWE-79 | 6.1–9.3 | A03:2021 |
| 03 | [Broken Access Control](part1-vulnerabilities/03-broken-access-control.md) | CWE-284 | 5.4–9.8 | A01:2021 |
| 04 | [Bypass 2FA / MFA](part1-vulnerabilities/04-bypass-2fa-mfa.md) | CWE-287 | 7.5–9.0 | A07:2021 |
| 05 | [Bypass OTP](part1-vulnerabilities/05-bypass-otp.md) | CWE-287/640 | 7.5–9.5 | A07:2021 |
| 06 | [Login, Reset & Registration](part1-vulnerabilities/06-login-reset-registration.md) | CWE-287/640/521 | 5.0–9.8 | A07:2021 |
| 07 | [SSTI](part1-vulnerabilities/07-ssti.md) | CWE-94 | 9.8 Critical | A03:2021 |
| 08 | [SSRF](part1-vulnerabilities/08-ssrf.md) | CWE-918 | 7.5–10.0 | A10:2021 |
| 09 | [CSRF](part1-vulnerabilities/09-csrf.md) | CWE-352 | 4.3–8.8 | A01:2021 |
| 10 | [XXE](part1-vulnerabilities/10-xxe.md) | CWE-611 | 7.5–9.8 | A05:2021 |
| 11 | [HTTP Request Smuggling](part1-vulnerabilities/11-http-request-smuggling.md) | CWE-444 | 7.5–9.8 | A05:2021 |
| 12 | [CRLF Injection](part1-vulnerabilities/12-crlf-injection.md) | CWE-93 | 5.4–7.5 | A03:2021 |
| 13 | [Insecure Deserialization](part1-vulnerabilities/13-insecure-deserialization.md) | CWE-502 | 8.1–9.8 | A08:2021 |
| 14 | [LDAP Injection](part1-vulnerabilities/14-ldap-injection.md) | CWE-90 | 7.5–9.8 | A03:2021 |
| 15 | [Unrestricted File Upload](part1-vulnerabilities/15-unrestricted-file-upload.md) | CWE-434 | 7.2–9.8 | A04:2021 |
| 16 | [Rate Limiting Bypass](part1-vulnerabilities/16-rate-limiting-bypass.md) | CWE-307/799 | 5.3–7.5 | A04:2021 |
| 17 | [Account Takeover (ATO)](part1-vulnerabilities/17-account-takeover.md) | CWE-287/620/640 | 8.0–10.0 | A07:2021 |
| 18 | [Remote Code Execution (RCE)](part1-vulnerabilities/18-rce.md) | CWE-78/94/502 | 9.8–10.0 | A03:2021 |
| 19 | [Business Logic Testing](part1-vulnerabilities/19-business-logic.md) | CWE-840/841 | Varies | A04:2021 |
| 20 | [Clickjacking](part1-vulnerabilities/20-clickjacking.md) | CWE-1021 | 4.3–7.5 | A05:2021 |
| 21 | [DOM-Based Vulnerabilities](part1-vulnerabilities/21-dom-vulnerabilities.md) | CWE-79/601/116 | 4.3–9.3 | A03:2021 |
| 22 | [CORS](part1-vulnerabilities/22-cors.md) | CWE-346 | 4.3–8.8 | A05:2021 |
| 23 | [OS Command Injection](part1-vulnerabilities/23-os-command-injection.md) | CWE-78 | 9.8 Critical | A03:2021 |
| 24 | [Path Traversal](part1-vulnerabilities/24-path-traversal.md) | CWE-22 | 7.5–9.8 | A01:2021 |
| 25 | [WebSockets Security](part1-vulnerabilities/25-websockets-security.md) | CWE-345/346 | 4.3–9.0 | A01:2021 |
| 26 | [Web Cache Poisoning](part1-vulnerabilities/26-web-cache-poisoning.md) | CWE-444 | 5.4–9.0 | A05:2021 |
| 27 | [Information Disclosure](part1-vulnerabilities/27-information-disclosure.md) | CWE-200/209/497 | 3.7–7.5 | A02:2021 |
| 28 | [HTTP Host Header Attacks](part1-vulnerabilities/28-http-host-header-attacks.md) | CWE-20 | 5.4–8.8 | A03:2021 |
| 29 | [OAuth Authentication Attacks](part1-vulnerabilities/29-oauth-attacks.md) | CWE-601/287 | 5.4–9.8 | A07:2021 |
| 30 | [JWT Attacks](part1-vulnerabilities/30-jwt-attacks.md) | CWE-347/345 | 7.5–9.8 | A07:2021 |
| 31 | [Prototype Pollution](part1-vulnerabilities/31-prototype-pollution.md) | CWE-1321 | 5.4–9.8 | A03:2021 |
| 32 | [GraphQL API Vulnerabilities](part1-vulnerabilities/32-graphql-vulnerabilities.md) | CWE-284/400 | 4.3–9.8 | API4:2023 |
| 33 | [Race Conditions](part1-vulnerabilities/33-race-conditions.md) | CWE-362 | 5.4–9.8 | A04:2021 |
| 34 | [NoSQL Injection](part1-vulnerabilities/34-nosql-injection.md) | CWE-943 | 7.5–9.8 | A03:2021 |
| 35 | [Web LLM Attacks](part1-vulnerabilities/35-web-llm-attacks.md) | CWE-20/74 | 5.0–9.5 | LLM Top 10:2025 |
| 36 | [Web Cache Deception](part1-vulnerabilities/36-web-cache-deception.md) | CWE-444/525 | 5.4–8.8 | A05:2021 |
| 37 | [Essential Skills & Recon](part1-vulnerabilities/37-essential-skills-recon.md) | — | — | All |

---

## 🎯 Part II — Domain-Specific Attack Surfaces

- [Banking Application — Web & Mobile](part2-attack-surfaces/banking-application.md)
- [API Penetration Testing](part2-attack-surfaces/api-penetration-testing.md)
- [Mobile Penetration Testing (Android & iOS)](part2-attack-surfaces/mobile-penetration-testing.md)
- [Source Code Review](part2-attack-surfaces/source-code-review.md)

---

## 🗺️ Part III — Full Methodologies

- [Web Pentest — Internal / External](part3-methodologies/web-pentest-internal-external.md)
- [Web Pentest — Gray Box & Black Box](part3-methodologies/web-pentest-gray-black-box.md)
- [Source Code Review Methodology](part3-methodologies/source-code-review-methodology.md)
- [Mobile Static & Dynamic Analysis](part3-methodologies/mobile-static-dynamic-analysis.md)
- [Banking Application Full Methodology](part3-methodologies/banking-application-methodology.md)

---

## ⚠️ Disclaimer

> Always test within **authorized scope**.  
> Follow **responsible disclosure** practices.  
> This encyclopedia is intended for **authorized penetration testers and security professionals** only.

---

*Happy Hunting. 🎯*
