# Banking Application Security

**Web • Android • iOS** | **High-Value Target** | **Regulatory Impact**

---

## Web Attack Surface

### Authentication & Session
- Login brute force, OTP bypass, 2FA bypass, session fixation, JWT manipulation
- Test OTP flows for both login AND fund transfers separately

### Authorization
- IDOR on account IDs, BAC on admin endpoints, horizontal/vertical privilege escalation

### Fund Transfers
- Negative amounts, race conditions, CSRF on transfer, beneficiary IDOR
- Test daily limit bypass via parallel sessions

### Input Validation
- SQLi in transaction filters, XSS in names/fields, SSTI in statements

### Business Logic
- Daily limit bypass, currency confusion, workflow skip, concurrent transactions
- Test negative transfer amounts — may credit instead of debit

### Information Disclosure
- Account details in error messages, verbose API responses
- Full PAN in response = PCI DSS Critical finding

---

## Mobile Attack Surface (Android & iOS)

- **Static:** API keys in APK/IPA, hardcoded credentials, insecure SharedPreferences, SSL pinning config
- **Dynamic:** SSL pinning bypass, traffic interception, root/jailbreak detection bypass, Frida hooks
- **Authentication:** Biometric bypass, OTP brute force in mobile API, insecure token storage
- **Data Storage:** Credentials in plaintext files, unencrypted SQLite DB, sensitive data in logs
- **IPC/Deep Links:** Intent injection, exported activities without permission, deep link parameter injection
- **WebView:** JavaScript bridge exploitation, `file://` URI access, `addJavascriptInterface` abuse

---

## Critical Banking-Specific Findings

| Finding | Severity |
|---------|----------|
| Unauthorized fund transfer via CSRF/IDOR/BAC | Critical |
| OTP bypass on transaction authorization | Critical |
| Negative transfer amount accepted | Critical |
| Full PAN returned in API response (PCI DSS) | Critical |
| Admin portal SQLi | Critical |
| Account balance/statement IDOR | High |
| Beneficiary addition without OTP | High |

---

## Reporting Tips

- Quantify financial impact: "This could allow unauthorized transfer of up to X amount."
- Reference: PCI DSS, SAMA CSF, NCA ECC-1, CBK regulations.
- PoC: demonstrate access but **NEVER** execute actual financial transactions.
- Severity floor: any OTP bypass on transfers = Critical. Any financial data IDOR = High minimum.
