# Banking Application Full Methodology

**Saudi Arabia Focus** | **SAMA CSF • NCA ECC-1 • PCI DSS**

---

## Phase 1 — Scope & Rules of Engagement

- Confirm in-scope: web domains, mobile app package names, API base URLs
- Confirm out-of-scope: production financial transactions, live customer data
- Obtain test accounts at all privilege levels: customer, teller, manager, admin
- Get test environment confirmation — **never** test on production without explicit authorization
- Confirm reporting timeline and emergency contact for critical findings

---

## Phase 2 — Web Application Testing

### Authentication Testing
```
□ Login brute force (no lockout?)
□ OTP bypass (in response? brute forceable? reusable? skippable?)
□ 2FA bypass (response manipulation? endpoint skip?)
□ Password reset (poisoning? token prediction? reuse?)
□ Session management (fixation? expiry? concurrent sessions?)
```

### Authorization Testing
```
□ IDOR on account IDs — change account_id in balance API
□ IDOR on transaction IDs — access other users' transactions
□ IDOR on statement downloads — access other users' PDFs
□ IDOR on beneficiary lists — view/modify other users' beneficiaries
□ Admin endpoint access with regular user token
```

### Business Logic Testing
```
□ Negative transfer amounts — does balance increase?
□ Zero amount transfers — any impact?
□ Overflow values — integer overflow on amounts?
□ Race condition on fund transfer — double spend?
□ Daily limit bypass — parallel sessions or distributed requests?
□ Currency/decimal confusion — 100 cents instead of 100 dollars?
□ Beneficiary addition without OTP — pre-authorize fraud?
```

### API Testing
```
□ OWASP API Top 10 across all endpoints
□ Mass assignment — send role, isAdmin, account_type in request body
□ GraphQL introspection (if applicable)
□ Hidden API versions — /v1 vs /v2 with different security controls
```

---

## Phase 3 — Mobile Application Testing

### Android
```bash
# Get APK
adb shell pm list packages | grep bankapp
adb shell pm path com.bank.app
adb pull /data/app/com.bank.app.apk ./

# Static analysis
jadx-gui com.bank.app.apk
grep -r 'api_key\|password\|secret\|token\|https://' ./sources/

# Dynamic analysis
objection -g com.bank.app explore --startup-command 'android sslpinning disable'
frida -U -f com.bank.app -l root_bypass.js
```

### iOS
```bash
# Extract IPA (jailbroken device)
frida-ios-dump com.bank.app

# Static analysis
grep -r 'NSAllowsArbitraryLoads\|api_key\|password' ./Payload/

# Dynamic analysis
objection -g com.bank.app explore --startup-command 'ios sslpinning disable'
keychain-dumper  # Extract keychain data
```

---

## Phase 4 — Regulatory Compliance Checks

### PCI DSS Checks
- [ ] Full PAN (card number) in API responses or logs
- [ ] CVV stored or returned in any form
- [ ] TLS version — reject TLS 1.0/1.1
- [ ] Encryption of cardholder data at rest
- [ ] Network segmentation for cardholder data environment

### SAMA CSF Checks
- [ ] Access control enforcement across all user types
- [ ] Audit logging for all sensitive operations (login, transfer, admin actions)
- [ ] Vulnerability management — is the app patched?
- [ ] Incident response procedures documented

### NCA ECC-1 Checks
- [ ] Cybersecurity controls alignment
- [ ] Data classification and protection
- [ ] Third-party risk management

---

## Phase 5 — Reporting (Banking Specific)

### Report Sections
1. **Executive Summary** — financial risk and regulatory exposure for CISO/Board
2. **Regulatory Compliance Summary** — PCI DSS / SAMA / NCA control violations
3. **Critical Findings** — immediate action items
4. **Detailed Findings** — full technical details with PoC
5. **Remediation Roadmap** — prioritized fix timeline

### Banking-Specific Severity Escalation

| Vulnerability | Base Severity | Banking Severity |
|---------------|--------------|-----------------|
| OTP bypass on login | High | **Critical** |
| OTP bypass on transfers | High | **Critical** |
| Financial data IDOR | Medium | **High** |
| Negative transfer accepted | Medium | **Critical** |
| Full PAN in response | High | **Critical (PCI DSS)** |
| CSRF on transfer | Medium | **High** |

### PoC Guidelines
- **Always:** Show the vulnerability is reproducible with exact steps
- **Always:** Capture request/response with Burp Suite
- **Never:** Execute actual fund transfers, even in test environment, without explicit written permission
- **Never:** Access real customer data
- **Document:** Financial impact estimate ("could allow unauthorized transfers of up to X")
