# 33 — Race Conditions

**CWE-362** | **CVSS: 5.4–9.8** | **OWASP A04:2021**

---

## What Is It?

Race conditions (TOCTOU) occur when multiple concurrent requests exploit a window between validation and action, allowing operations to execute multiple times or out of intended order.

> **Core Problem:** Non-atomic operations allow concurrent requests to bypass single-use or sequential constraints.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Limit Overrun** | Apply same coupon/credit multiple times simultaneously — all pass validation before any records use. |
| **Double Spend** | Initiate two fund transfers simultaneously — both pass balance check before deduction. |
| **TOCTOU** | Check permission → race window → use resource before check is enforced. |
| **Account Creation Race** | Register same username/email simultaneously — creates duplicate accounts. |
| **File Upload Race** | Upload malicious file → race window between upload and security scan → execute before scan. |

---

## Hunting Methodology

1. Identify single-use operations: coupon codes, referral bonuses, OTP validation, one-time links.
2. Identify limit-enforced operations: fund transfers (balance check), ticket purchases, discount application.
3. Use Turbo Intruder with 'gate' technique: queue all requests, release simultaneously.
4. Use Burp Repeater: open multiple tabs, send all at once via 'Send group (parallel)'.
5. Test with 10–50 concurrent requests — sufficient to trigger most race windows.
6. Banking: double-spend test on fund transfer — initiate two identical transfers simultaneously.

---

## Tips & Tricks

- **#1** Turbo Intruder 'gate': queue all requests → send gate signal → all release simultaneously.
- **#2** Race window varies by server load — run tests multiple times for reliability.
- **#3** Banking double-spend: always confirm with logs — never actually transfer money in PoC.
- **#4** Idempotency keys: some APIs use these to prevent races — check if key can be manipulated.

---

## Deadly Chains

```
Race condition + coupon code → unlimited discount → financial loss
Race condition + fund transfer → double-spend → account balance inconsistency
Race condition + OTP → bypass MFA via concurrent submission
Race condition + file upload → execute before antivirus scan → RCE
```
