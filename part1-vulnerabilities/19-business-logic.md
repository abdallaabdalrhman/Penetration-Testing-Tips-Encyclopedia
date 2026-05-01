# 19 — Business Logic Testing

**CWE-840/841** | **CVSS: Varies** | **OWASP A04:2021**

---

## What Is It?

Business logic vulnerabilities are flaws in the design and implementation of application workflows that allow unintended operations. Scanners cannot detect these — they require human reasoning.

> **Core Problem:** Application logic correctly implemented technically but fails to enforce business constraints.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Price Manipulation** | Modify item price, discount, or quantity in request — pay less than intended. |
| **Workflow Bypass** | Skip mandatory steps: checkout → payment → skip to order confirmation. |
| **Negative Value Abuse** | Enter negative quantities or amounts — reverse charges or receive credit. |
| **Race Condition Abuse** | Concurrent requests exploit TOCTOU — apply coupon twice simultaneously. |
| **Fund Transfer Logic** | Negative transfer amount → receive money instead of sending. |

---

## Hunting Methodology

1. Understand the application's intended business flow end-to-end.
2. Identify all state-changing operations: orders, payments, transfers, upgrades.
3. Test every numeric field with: `0`, `-1`, `-100`, `99999`, overflow values.
4. Test workflow step bypassing: skip step 2, go directly to step 3.
5. Test race conditions: submit the same action multiple times concurrently.
6. Banking: test transfer amounts (negative, zero, overflow), beneficiary limits, daily limits.
7. Test currency/unit manipulation: change USD to units, change decimal positions.
8. Test loyalty points, cashback, referral bonus for abuse scenarios.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite Repeater** | Manual parameter manipulation and workflow step testing. |
| **Turbo Intruder** | Race condition exploitation with concurrent requests. |
| **Custom Python scripts** | Automated race condition testing with threading. |

---

## Tips & Tricks

- **#1** Business logic bugs need human reasoning — always think 'what should happen vs what does happen?'
- **#2** Negative values in banking: `-100` USD transfer may add 100 to attacker. Always test.
- **#3** Race conditions: Turbo Intruder with 'gate' feature for simultaneous request release.
- **#4** Banking: test daily limit bypass by spreading transfers or using parallel sessions.

---

## Deadly Chains

```
Price manipulation + race condition → purchase items for free
Negative transfer + no server-side validation → reverse banking fraud
Workflow bypass + payment skip → free product/service
Race condition on coupon + unlimited use → major financial loss for platform
```
