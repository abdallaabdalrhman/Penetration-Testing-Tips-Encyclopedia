# Mobile Static & Dynamic Analysis Methodology

---

## Android Static Analysis

```bash
# Step 1 — Decompile
apktool d app.apk -o app_decompiled/
jadx -d app_java/ app.apk

# Step 2 — Secrets hunting
grep -rn "api_key\|api_secret\|password\|token\|AWS\|firebase" app_java/
grep -rn "http://\|https://" app_java/ | grep -v "schema\|xmlns"

# Step 3 — Manifest analysis
cat app_decompiled/AndroidManifest.xml | grep -E "exported|debuggable|allowBackup|permission"

# Step 4 — Crypto analysis
grep -rn "MD5\|SHA1\|DES\|ECB\|AES/ECB" app_java/
grep -rn "SecretKeySpec\|IvParameterSpec" app_java/

# Step 5 — Network security config
cat app_decompiled/res/xml/network_security_config.xml
```

## Android Dynamic Analysis

```bash
# Setup
adb connect 192.168.1.x:5555  # emulator
# Install Burp cert: Settings → Security → Install certificate

# SSL Pinning Bypass
objection -g com.target.app explore
android sslpinning disable

# Root detection bypass
frida -U -f com.target.app -l scripts/root_bypass.js --no-pause

# Monitor traffic
adb logcat | grep -i "error\|exception\|token\|key\|password"

# File system monitoring
adb shell run-as com.target.app ls -la /data/data/com.target.app/
```

## iOS Static Analysis

```bash
# Extract IPA (jailbroken)
frida-ios-dump -u root -H 192.168.1.x com.target.app

# Unpack and analyze
unzip app.ipa -d app_unpacked/
strings app_unpacked/Payload/App.app/App | grep -i "key\|secret\|pass\|token"

# Check Info.plist
cat app_unpacked/Payload/App.app/Info.plist | grep -A1 "NSAllowsArbitraryLoads"

# Binary analysis
otool -L app_unpacked/Payload/App.app/App  # Linked libraries
```

## iOS Dynamic Analysis

```bash
# SSL Pinning Bypass
objection -g com.target.app explore
ios sslpinning disable

# Keychain inspection
keychain-dumper

# File system
ls /var/mobile/Containers/Data/Application/[UUID]/Documents/
ls /var/mobile/Containers/Data/Application/[UUID]/Library/
```
