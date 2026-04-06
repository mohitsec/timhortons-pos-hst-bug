# 🔍 Tim Hortons POS — HST Tax Logic Bug (Responsible Disclosure)

> **Type:** Financial Logic / Tax Compliance Bug  
> **System:** Stingray 4 POS by Jtech/PAX (JS 980)  
> **Severity:** High — Financial & CRA Tax Compliance Impact  
> **Status:** Reported & Submitted ✅  
> **Discovered by:** [Mohit Prajapati](https://github.com/mohitsec)

---

## 📋 Summary

While working as a Shift Leader at Tim Hortons, I identified a tax logic defect in the **Stingray 4 POS system (JS 980)** where partial refunds on donut orders incorrectly applied HST that was **never collected at the time of sale** — resulting in customers receiving more money back than they originally paid.

This is a real-world example of a **financial compliance vulnerability** discovered through careful observation, documented with evidence, and reported through proper responsible disclosure channels.

---

## 🐛 The Bug

### Background — CRA Donut Tax Rule
Under Canadian tax law, **baked goods sold in quantities greater than 5 are HST-exempt** at point of sale. The Tim Hortons POS correctly applies this rule during purchase — no HST is charged when a customer buys 6 or more donuts.

### What Goes Wrong on Refund
When a **partial refund** is processed for items from a tax-exempt order, the POS refund module **independently recalculates tax** based on the item price — ignoring the tax treatment of the original transaction entirely.

### Result
| | Purchase | Refund |
|---|---|---|
| Items | 5x Boston Cream donuts | 5x Boston Cream donuts (partial refund) |
| Subtotal | $8.45 | $8.45 |
| HST | $0.00 ✅ (correctly exempt) | $1.10 ❌ (never collected) |
| **Total** | **$8.45** | **$9.55** |

**The customer receives $1.10 more than they originally paid.**

---

## 📊 Transaction Evidence

Two POS transactions captured on **March 20, 2025 at 07:28 PM (Shift 3)**:

**Order #187 — Original Purchase**
- Items: 5x Boston Cream + Assorted ½ Dozen (11 donuts total)
- Total: $16.44
- HST: $0.00 (correct — CRA exemption applied)
- Tax breakdown: HST $0.00 | HST1 $0.00

**Order #250 — Partial Refund**
- Items: 5x Boston Cream refunded
- Subtotal: $8.45
- HST applied: $1.10 (**incorrect** — was never collected)
- Tax breakdown: HST $0.42 | HST1 $0.68
- Refund total: $9.55 (**$1.10 over-refund**)

> 📁 Evidence screenshots are retained privately and submitted directly to Tim Hortons and the POS vendor as part of the formal bug report.

---

## 💥 Impact Assessment

### Per-Transaction
- Store absorbs a **$1.10 direct financial loss** per affected refund
- Customer receives more money back than they paid

### Systemic Risk
- The Stingray 4 POS is deployed across **thousands of Tim Hortons locations** across Canada
- If the same firmware version is affected systemwide, cumulative financial losses could be significant
- Incorrect HST on refunds could affect corporate **HST remittance reporting to CRA**

### Compliance Angle
- Under Canada's *Excise Tax Act*, businesses must only refund HST that was actually collected
- Refunding HST that was never remitted creates an imbalance in the company's **Input Tax Credit (ITC) reporting**
- This is a potential **CRA tax compliance exposure** at the corporate level

---

## 🔧 Root Cause Analysis

The refund calculation module in the Stingray 4 firmware appears to:

1. Take the item price at time of refund
2. Apply the standard tax calculation independently
3. **Not reference** the tax fields from the original transaction

This means the sale path and the refund path use **different tax logic**, creating a discrepancy whenever the original sale was tax-exempt.

```
SALE PATH:
  Item price → Check quantity → >5 donuts? → Apply HST exemption → Total: $8.45

REFUND PATH (buggy):
  Item price → Apply standard tax rate → Total: $9.55 ❌
  (never checks original transaction tax fields)
```

---

## ✅ Recommended Fix

1. **Refund logic should reference original transaction tax** — not recalculate independently
2. If original HST on a line item was $0.00, the refund must also apply $0.00 HST
3. Add a validation check: refund total must never exceed original amount paid for those items
4. Deploy patched firmware across all affected Stingray 4 / JS 980 units

---

## 📬 Responsible Disclosure Timeline

| Date | Action |
|---|---|
| March 20, 2025 | Bug discovered during Shift 3 |
| March 20, 2025 | Photographic evidence captured (Order #187 + Order #250) |
| March 20, 2025 | Formal bug report written with full documentation |
| March 20, 2025 | Submitted to Tim Hortons via official website |
| March 20, 2025 | Reported to POS vendor (Jtech/PAX Technology) |

---

## 🧠 Skills Demonstrated

- **Financial compliance awareness** — identified a tax logic error with real CRA implications
- **Structured vulnerability documentation** — formal bug report with reproduction steps, root cause, and recommended fix
- **Responsible disclosure** — reported through proper channels before any public disclosure
- **Evidence collection** — captured timestamped photographic proof of both transactions
- **Impact analysis** — assessed per-transaction, systemic, and regulatory risk

---

## 👤 About

**Mohit Prajapati**  
Cybersecurity Operations | CompTIA Security+ | (ISC)² CC | IBM Cybersecurity Analyst  
York University — Postgraduate Certificate in Cybersecurity Operations (2024)

🔗 [github.com/mohitsec](https://github.com/mohitsec)

---

*This disclosure is made in good faith for transparency and professional documentation purposes. All evidence was submitted directly to the affected parties prior to publication.*
