# Source Code Review Methodology

---

## Phase 1 — Setup & Understanding

```bash
# Clone and explore
git clone https://github.com/target/repo
git log --oneline -20  # Recent commits
git log --all --oneline | head -50  # Full history

# Find secrets in git history
truffleHog --regex --entropy=True ./
gitleaks detect --source . -v
```

## Phase 2 — Automated Scanning

```bash
# Semgrep — multi-language SAST
semgrep --config=p/security-audit --config=p/owasp-top-ten ./

# Language-specific
bandit -r ./  # Python
brakeman ./  # Rails
eslint --plugin security ./  # JavaScript
```

## Phase 3 — Manual Review Priorities

1. **Entry Points** — all routes, API endpoints, file uploads, WebSocket handlers
2. **Authentication** — login, session creation, token validation, 2FA checks
3. **Authorization** — access control checks on each resource/action
4. **Dangerous Functions** — trace backwards from dangerous sinks to user input
5. **Cryptography** — key storage, algorithm selection, random number generation
6. **Third-party integrations** — API calls, webhook handlers, data deserialization

## Phase 4 — Dataflow Analysis

```
User Input → [Processing] → Dangerous Sink

Sources: request.params, request.body, request.headers, cookies, DB data, file content
Sinks: SQL queries, OS commands, eval(), innerHTML, file paths, template rendering
```
