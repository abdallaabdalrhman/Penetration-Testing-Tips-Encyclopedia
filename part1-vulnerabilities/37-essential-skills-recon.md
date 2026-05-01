# 37 — Essential Skills & Recon Techniques

**Core Competency** | **All Domains** | **Pre-engagement Foundation**

---

## What Is It?

Essential skills form the foundation of effective bug bounty hunting and penetration testing — from reconnaissance methodology to PoC writing, report quality, and vulnerability chaining thinking.

> **Core Problem:** Technical skills alone aren't enough — methodology, tooling, and reporting determine success.

---

## Recon Methodology

### Step 1 — Subdomain Enumeration
```bash
subfinder -d target.com -o subs.txt
amass enum -d target.com >> subs.txt
assetfinder target.com >> subs.txt
cat subs.txt | dnsx | httpx -o live.txt
```

### Step 2 — JS File Mining
```bash
cat live.txt | gau | grep "\.js$" | tee js_files.txt
cat js_files.txt | xargs -I{} python3 LinkFinder.py -i {} -o cli
cat js_files.txt | xargs -I{} python3 SecretFinder.py -i {}
```

### Step 3 — Directory Brute Force
```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt \
  -u https://target.com/FUZZ -mc 200,301,302,403
```

### Step 4 — Parameter Discovery
```bash
arjun -u https://target.com/endpoint --rate 10
# Use Param Miner in Burp Suite passively
```

### Step 5 — Automated Scanning
```bash
nuclei -l live.txt -t all -o nuclei_results.txt
```

---

## Essential Tools Arsenal

| Category | Tool | Purpose |
|----------|------|---------|
| **Recon** | subfinder / amass | Subdomain enumeration |
| **Recon** | httpx | HTTP probe — alive subdomains with status codes |
| **Recon** | gau / waybackurls | Fetch all known URLs for a domain |
| **Fuzzing** | ffuf | Fast web fuzzer for directories, parameters, vhosts |
| **Scanning** | Nuclei | Template-based vulnerability scanner — 1000+ templates |
| **Proxy** | Burp Suite Pro | Core proxy, scanner, Collaborator, extensions |
| **Wordlists** | SecLists | Comprehensive wordlists for all fuzzing tasks |
| **Discovery** | Shodan / Censys / FOFA | Internet-wide scan data for attack surface discovery |

---

## Vulnerability Chaining Mindset

```
For every finding, ask: "What can I do with this?"

IDOR (email leak)
  + Password Reset
  = Full ATO → Critical

Information Disclosure (internal IP)
  + SSRF
  = Internal Service Access → High/Critical

Self-XSS
  + CSRF
  = Exploitable XSS → Medium/High
```

---

## PoC Development Guidelines

1. **Reproduce before reporting** — always verify the bug yourself
2. Include exact request/response with screenshots
3. Demonstrate real-world impact safely — no destructive actions
4. For banking: show account access but **never** execute actual transfers

---

## Report Writing Template

```
Title: [What vulnerability] in [Where] allows [Impact]
Severity: Critical/High/Medium/Low (with CVSS score)
CWE: CWE-XXX
OWASP: A0X:2021

Description:
  Brief explanation of the vulnerability and root cause.

Impact:
  Business impact in non-technical terms.

Steps to Reproduce:
  1. Step one
  2. Step two
  3. Observe [impact]

PoC:
  [Screenshots / video / request-response]

Remediation:
  Specific, actionable fix recommendation.
```

---

## Tips & Tricks

- **#1** Recon is everything: 80% of bounties come from good recon finding missed subdomains/endpoints.
- **#2** JS files are gold: extract all URLs, API keys, internal endpoints from minified JS.
- **#3** Chain everything: IDOR + info disclosure + reset poisoning = Critical ATO.
- **#4** Speed matters in bug bounty: set up automated recon pipeline that runs on all new targets.
- **#5** Quality over quantity: 1 well-written Critical > 10 poorly-written Mediums.
- **#6** Read disclosed reports daily: HackerOne Hacktivity — learn from others' methodologies.
- **#7** Carefully read scope — test every in-scope asset, note out-of-scope carefully.

---

## Deadly Chains

```
Recon finds forgotten subdomain → unauthenticated endpoint → data breach
JS file → hardcoded API key → cloud storage access → all customer data
IDOR + info disclosure + password reset poisoning → Critical ATO chain
Subdomain takeover → serve malicious content on legitimate domain → phishing/XSS
```
