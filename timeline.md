# Disclosure Timeline — Tim Hortons POS HST Bug

> **THPOS-2025-001** | Discovered: March 20, 2025 | Status: Submitted ✅

---

## Timeline

```
March 20, 2025
│
├── 07:28 PM — Bug Discovered
│   └── During normal Shift 3 operations, noticed a refund total
│       exceeded the original purchase amount on a donut order.
│       Recognized this as abnormal POS behavior.
│
├── 07:28 PM — Evidence Captured
│   └── Photographed both POS screens before the session closed:
│       • Order #187 — original purchase (HST: $0.00)
│       • Order #250 — partial refund (HST: $1.10 incorrectly applied)
│       Both screenshots timestamped and retained securely.
│
├── Post-Shift — Root Cause Investigation
│   └── Analyzed the discrepancy:
│       • Confirmed original order was HST-exempt (>5 donuts, CRA rule)
│       • Confirmed refund module applied independent tax calculation
│       • Identified the refund path does not reference original
│         transaction tax fields
│       • Calculated over-refund: $1.10 per occurrence
│
├── Post-Shift — Formal Bug Report Written
│   └── Documented the bug with:
│       • Reproduction steps
│       • Transaction evidence (Order #187 and #250)
│       • Root cause analysis
│       • Business and compliance impact assessment
│       • Recommended fix
│
├── March 20, 2025 — Submitted to Tim Hortons
│   └── Bug report and evidence submitted via Tim Hortons
│       official website contact/feedback channel.
│       Flagged as operational and financial compliance issue.
│
└── March 20, 2025 — Submitted to POS Vendor
    └── Report submitted to Jtech/PAX Technology (manufacturer
        of the Stingray 4 / JS 980 POS terminal) for firmware
        investigation and patch development.
```

---

## Responsible Disclosure Principles Followed

| Principle | Action Taken |
|---|---|
| Discovery without exploitation | Bug discovered incidentally; not triggered intentionally |
| Evidence collection | Timestamped photographic proof captured before reporting |
| Internal reporting first | Reported to Tim Hortons before any public disclosure |
| Vendor notification | POS manufacturer notified directly |
| No public disclosure before submission | This repo published after both reports were submitted |
| Documentation | Full structured bug report prepared |

---

## Current Status

- ✅ Discovered and documented
- ✅ Submitted to Tim Hortons
- ✅ Submitted to POS vendor (Jtech/PAX)
- ⏳ Awaiting acknowledgment and fix confirmation
- ⏳ Firmware patch pending

---

*Maintained by [Mohit Prajapati](https://github.com/mohitsec)*
