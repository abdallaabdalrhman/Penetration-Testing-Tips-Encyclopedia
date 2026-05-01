# Source Code Review

**Manual Review • Automated SAST • Regex Hunting** | **All Languages**

---

## High-Value Grep Patterns

```bash
# SQL Injection
grep -rn "query.*\$_GET|query.*\$_POST|query.*+.*req\." ./

# Command Injection
grep -rn "os\.system\(|subprocess\.call\(|exec\(|shell_exec\(" ./

# Hardcoded Secrets
grep -rn "password\s*=\s*[\"'][^\"']{4,}[\"']" ./

# Insecure Deserialization
grep -rn "unserialize\(|pickle\.loads\(|Marshal\.load\(" ./

# SSRF
grep -rn "file_get_contents\(\$|curl_exec\(|requests\.get\(.*user" ./

# XXE
grep -rn "DOMParser\(\)|XMLReader\(\)|DocumentBuilderFactory" ./

# Path Traversal
grep -rn "\.\./|open\(.*\.\.\." ./

# Weak Crypto
grep -rn "MD5\(|SHA1\(|DES\.|ECB|createCipher\(" ./
```

---

## SAST Tools

| Tool | Language | Notes |
|------|----------|-------|
| **Semgrep** | All | Fast, customizable — write custom rules for any language |
| **SonarQube** | All | Enterprise SAST with multi-language support and dashboard |
| **CodeQL** | All | GitHub's semantic code analysis — deep dataflow analysis |
| **Bandit** | Python | Python security linter |
| **Brakeman** | Ruby/Rails | Rails-specific SAST scanner |
| **ESLint Security** | JavaScript/Node.js | JavaScript/Node.js security linting |
| **Trivy** | All | Container and dependency vulnerability scanning |

---

## Source Code Review Methodology

### Phase 1 — Understand the Application
- Map the tech stack: language, framework, database, external services
- Identify authentication mechanism and session management
- Map all data flows from input to output

### Phase 2 — Automated Scanning
```bash
# Run Semgrep with security rules
semgrep --config=p/security-audit ./

# Run Bandit for Python
bandit -r ./app -f html -o bandit_report.html

# Run CodeQL
codeql database create my-database --language=python
codeql database analyze my-database python-security-extended.qls
```

### Phase 3 — Manual Review Focus Areas
1. **Authentication & Authorization** — trace all auth checks
2. **Input validation** — find all user-controlled inputs, trace to dangerous sinks
3. **Cryptographic implementations** — key management, algorithm choices
4. **Third-party integrations** — unvalidated data from external APIs
5. **Error handling** — verbose errors exposing internals

### Phase 4 — Dangerous Sink Patterns

```python
# Python — dangerous sinks to trace from user input
os.system()
subprocess.call()
eval()
exec()
pickle.loads()
yaml.load()  # use yaml.safe_load() instead
open() with user-controlled path
```

```javascript
// JavaScript — dangerous sinks
eval()
innerHTML
document.write()
setTimeout(userInput)
setInterval(userInput)
location.href = userInput
child_process.exec()
```

```php
// PHP — dangerous sinks
system()
exec()
shell_exec()
eval()
unserialize()
include($userInput)
require($userInput)
```
