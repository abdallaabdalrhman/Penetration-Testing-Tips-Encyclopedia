# 15 — Unrestricted File Upload

**CWE-434** | **CVSS: 7.2–9.8** | **OWASP A04:2021**

---

## What Is It?

Unrestricted file upload vulnerabilities allow attackers to upload malicious files (webshells, malware, HTML/SVG for XSS) to execute server-side code or attack users.

> **Core Problem:** Server trusts client-supplied file content and extension without proper validation.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Webshell Upload (RCE)** | Upload `.php`/`.asp`/`.jsp` — access via URL → execute OS commands. |
| **Extension Bypass** | Rename `.php` to `.php5`, `.phtml`, `.php.jpg` — server executes anyway. |
| **Magic Bytes Bypass** | Prepend `GIF89a;` to PHP code — passes image validation. |
| **SVG XSS** | SVG with `<script>` — rendered in browser → XSS for viewers. |
| **ZIP Slip** | Zip containing `../../../shell.php` — extract traverses directories. |

---

## Hunting Methodology

1. Find all file upload endpoints: avatar, document, profile photo, report upload, import.
2. Test direct upload of `.php` file — does server execute it?
3. Bypass extension check: `.php5`, `.php7`, `.phtml`, `.shtml`, `.phar`, `.php.jpg`.
4. Bypass MIME check: change `Content-Type` to `image/jpeg` while keeping `.php` extension.
5. Magic bytes: prepend `GIF89a;` or PNG header to PHP webshell.
6. Test path traversal in filename: `../../../var/www/html/shell.php`.
7. Upload SVG with XSS: `<svg><script>alert(document.cookie)</script></svg>`.
8. Banking: document upload (KYC), statement import, profile photo upload.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite Repeater** | Modify `Content-Type`, extension, magic bytes during upload. |
| **Weevely** | PHP webshell generator and manager. |
| **ExifTool** | Inject code into image metadata (EXIF XSS). |

---

## Tips & Tricks

- **#1** Always try double extension: `.php.jpg`, `.php%00.jpg`, `.php;.jpg`.
- **#2** SVG XSS is often overlooked — any SVG display surface is vulnerable to XSS.
- **#3** Banking KYC uploads: document validation is often weak — test all bypass techniques.

---

## Deadly Chains

```
File upload → RCE → server compromise → internal network access → DB exfil
SVG upload → XSS → session cookie theft → ATO
File upload + SSRF (SVG) → cloud metadata → IAM credentials
```
