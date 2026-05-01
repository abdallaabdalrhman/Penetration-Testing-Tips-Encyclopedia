# 08 — Server-Side Request Forgery (SSRF)

**CWE-918** | **CVSS: 7.5–10.0** | **OWASP A10:2021**

---

## What Is It?

SSRF forces the server to make HTTP requests to attacker-controlled locations, including internal services, cloud metadata APIs, and localhost. Critical in cloud environments.

> **Core Problem:** Server fetches user-supplied URLs without validating destination.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Basic SSRF** | Server fetches `http://internal-host:8080/admin` — attacker accesses internal services. |
| **Blind SSRF** | No response returned — detect via DNS/HTTP callback (Burp Collaborator). |
| **SSRF via Redirect** | URL redirects to internal address — bypass allow-list via open redirect. |
| **SSRF in PDF/Image Generation** | Server renders URL to image/PDF — provide internal URL. |
| **Cloud Metadata SSRF** | `http://169.254.169.254/` (AWS/GCP) → IAM credentials, instance info. |

---

## Hunting Methodology

1. Find URL input points: webhooks, image URLs, PDF URLs, social media preview, import from URL.
2. Test basic: `http://127.0.0.1/`, `http://localhost/`, `http://0.0.0.0/`
3. Test cloud metadata: `http://169.254.169.254/latest/meta-data/` (AWS), `http://metadata.google.internal/`
4. Probe internal network: `http://10.0.0.1/`, `http://192.168.1.1/` — scan for open ports.
5. Blind SSRF: use Burp Collaborator or interactsh URL — check for DNS/HTTP callbacks.
6. Bypass filters: `http://2130706433/` (decimal IP), `http://0x7f000001/` (hex), DNS rebinding.
7. Test against common internal ports: 22, 80, 443, 8080, 8443, 6379 (Redis), 9200 (Elasticsearch).
8. Banking: webhook URLs, callback URLs, payment gateway integration URLs.
9. Test IPv6: `http://[::1]/`, `http://[::ffff:127.0.0.1]/`

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Collaborator** | Blind SSRF detection via DNS/HTTP callback. |
| **SSRFmap** | Automated SSRF exploitation framework. |
| **Gopherus** | Generate SSRF payloads for exploiting internal services (Redis, MySQL, etc.). |
| **ffuf** | Internal port scanning via SSRF. |
| **interactsh** | Open-source out-of-band interaction server. |

---

## Tips & Tricks

- **#1** Cloud metadata is the crown jewel: `169.254.169.254` → IAM → full cloud account.
- **#2** Bypass `127.0.0.1` blocks: `localhost`, `0.0.0.0`, `0177.0.0.1`, `0x7f000001`, `2130706433`.
- **#3** SSRF + Redis (port 6379): Gopherus → SSRF payload → write webshell via Redis.
- **#4** SSRF in mobile app: intercept webhook/callback setup — banking payment callbacks.
- **#5** DNS rebinding: bypass IP allow-lists — first DNS → allowed IP, then rebind to `127.0.0.1`.

---

## Deadly Chains

```
SSRF → AWS metadata (169.254.169.254) → IAM credentials → S3/EC2 full access

SSRF → Redis → RESP protocol → RCE via module load or cron injection

SSRF → Internal admin panel (no auth) → admin takeover

SSRF + XXE in XML upload → retrieve /etc/passwd, internal URLs

Blind SSRF → Collaborator hit → pivot to internal microservices
```
