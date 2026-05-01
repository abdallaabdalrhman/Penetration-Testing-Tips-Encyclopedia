# Mobile Penetration Testing

**Android • iOS** | **Static & Dynamic Analysis** | **OWASP MASVS**

---

## Before You Start

- Define scope: Obtain APK/IPA files, credentials (test accounts), API docs, and backend details.
- Set up environment: Rooted/jailbroken devices or emulators, proxy tools (Burp Suite).
- Create test accounts at all privilege levels: guest, regular user, premium, admin.
- Document all API endpoints found during static analysis before dynamic testing.

---

## Static Analysis

```bash
# Decompile APK
apktool d app.apk
jadx-gui app.apk

# Search for hardcoded secrets
grep -r 'api_key|password|secret|token|AWS|firebase' ./

# Check AndroidManifest
cat AndroidManifest.xml | grep -i 'exported|debuggable|allowBackup'
```

### Key Checks
- **AndroidManifest.xml:** `exported=true` activities/services, `debuggable=true`, `allowBackup=true`
- **iOS Info.plist:** `NSAllowsArbitraryLoads: true` (ATS disabled) → cleartext HTTP traffic
- **Cryptography:** Weak algorithms (MD5, DES, ECB mode), hardcoded IV/keys
- **Firebase:** Extract firebase URL → test `/.json` endpoint without authentication
- **Third-party libraries:** Check Podfile.lock / build.gradle for known CVEs

---

## Local Data Storage Analysis

```bash
# Android — pull app data
adb shell
cd /data/data/com.app.package/
sqlite3 databases/app.db

# Check logs for sensitive data
adb logcat | grep -i 'token|password|session|error'
```

### Key Checks
- Shared Preferences / UserDefaults for plaintext sensitive data
- Unencrypted SQLite databases
- Sensitive data in log files
- iOS Keychain: verify correct accessibility flags (not `kSecAttrAccessibleAlways`)
- Screenshot leaks: verify app clears sensitive screens on backgrounding

---

## Network & Traffic Analysis

```bash
# SSL Pinning Bypass (Android)
objection -g com.app.id explore --startup-command 'android sslpinning disable'

# Root/Jailbreak Detection Bypass
frida -U -f com.app.id -l bypass_root.js

# Traffic Interception
# Install Burp CA cert → Configure proxy → Intercept
```

---

## Authentication & Session Testing

- [ ] Biometric bypass via Frida hooks
- [ ] JWT algorithm confusion (`alg:none`, RS256→HS256)
- [ ] OAuth flows: `redirect_uri` manipulation, state bypass, PKCE
- [ ] Session timeout and forced logout — does server invalidate tokens?
- [ ] OTP brute force on mobile API (often weaker implementation)
- [ ] Remember me functionality — token entropy and expiry

---

## Mobile Tools Arsenal

| Tool | Purpose |
|------|---------|
| **apktool** | Decompile / recompile APK (smali + resources) |
| **jadx / jadx-gui** | Decompile APK to readable Java source code |
| **MobSF** | Automated mobile security framework — static + dynamic analysis |
| **Frida** | Dynamic instrumentation toolkit — hook any method at runtime |
| **objection** | Frida-based runtime exploration — SSL/root bypass one-liners |
| **Burp Suite** | Intercept, modify, replay all mobile API traffic |
| **drozer** | Android component security assessment — intent fuzzing |
| **ADB** | Android Debug Bridge — shell, logcat, file pull, backup |
| **SSL Kill Switch 2** | iOS SSL pinning bypass on jailbroken device |
| **keychain-dumper** | Extract iOS keychain items on jailbroken device |
