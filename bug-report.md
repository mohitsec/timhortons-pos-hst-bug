# Bug Report — Tim Hortons POS HST Tax Logic Error

**Report ID:** THPOS-2025-001  
**Date:** March 20, 2025  
**Reporter:** Mohit Prajapati, Shift Leader  
**System:** Stingray 4 POS (JS 980) by Jtech/PAX Technology  
**Severity:** HIGH  
**Status:** Submitted to Tim Hortons & POS Vendor  

---

## 1. Executive Summary

A tax calculation logic defect exists in the Stingray 4 POS refund module. When a customer purchases more than 6 donuts (making the order HST-exempt under CRA rules), and subsequently requests a partial refund, the system incorrectly applies HST to the refund amount. The HST applied on the refund was **never collected at the time of sale**, resulting in the customer receiving a refund larger than the original amount paid.

---

## 2. Bug Details

| Field | Value |
|---|---|
| Report Date | March 20, 2025 |
| Reported By | Mohit Prajapati, Shift Leader |
| POS System | Stingray 4 by JS 980 (Jtech/PAX) |
| Shift | Shift 3 |
| Severity | HIGH — Financial & Tax Compliance Impact |
| Bug Type | Tax calculation logic error (refund path vs. sale path) |
| Status | Unresolved — Submitted for investigation |

---

## 3. Steps to Reproduce

1. Ring in an order containing **more than 6 donuts** (e.g., 11 donuts total)
2. Confirm that **HST = $0.00** is applied at checkout — this is correct per CRA food exemption rules
3. Process a **partial refund** for a subset of those donuts (e.g., 5 donuts = $8.45)
4. **Observe:** The refund screen applies $1.10 HST, resulting in a $9.55 refund on an $8.45 purchase

---

## 4. Transaction Evidence

Both transactions captured March 20, 2025 at 07:28 PM, Shift 3.

### Order #187 — Original Purchase
```
Items:    5x Boston Cream + Assorted 1/2 Dozen (11 donuts)
Subtotal: $16.44
Tax:      HST $0.00 | HST1 $0.00
Total:    $16.44
Change:   $0.00
```
**Result: Correct — CRA tax exemption applied, no HST charged.**

### Order #250 — Partial Refund
```
Items:    5x Boston Cream (partial refund from original order)
Subtotal: $8.45
Tax:      HST $0.42 | HST1 $0.68 = $1.10 TOTAL TAX
Total:    $9.55
Change:   $0.00
```
**Result: INCORRECT — $1.10 HST applied that was never collected.**

### Discrepancy
```
Amount customer paid for 5x Boston Cream:    $8.45
Amount customer receives as refund:          $9.55
Over-refund per transaction:                 $1.10
```

---

## 5. Expected vs. Actual Behavior

### Expected
The POS refund module should reference the tax fields from the original transaction. Since the original order applied $0.00 HST, the refund should also apply $0.00 HST. The refund amount should equal exactly what the customer paid for those items: **$8.45**.

### Actual
The POS refund module independently recalculates tax based on the item price at time of refund, without checking the original transaction's tax treatment. This produces a refund of **$9.55** — $1.10 more than the customer paid.

---

## 6. Root Cause Analysis

The suspected root cause is a **logic separation between the sale path and the refund path** in the Stingray 4 firmware:

**Sale path (correct):**
```
Item price → Quantity check → >5 donuts? → Apply HST exemption (Rule: CRA baked goods) → Final price
```

**Refund path (defective):**
```
Item price → Apply standard tax rate → Final refund amount
(Does NOT reference original transaction tax fields)
```

The refund module treats every refunded item as a fresh transaction rather than an inverse of the original. When the original sale was tax-exempt, this discrepancy surfaces as an over-refund.

---

## 7. Impact Assessment

### Financial — Per Transaction
- Store absorbs a direct loss of **$1.10 per affected refund**
- Customer receives more money than they paid

### Financial — Systemic
- Stingray 4 POS deployed across thousands of Tim Hortons locations in Canada
- If this firmware version is consistent across the estate, cumulative losses are material
- No mechanism currently in place to detect or flag these over-refunds

### Regulatory — CRA Compliance
- Under Canada's *Excise Tax Act*, HST may only be refunded if it was originally collected
- Refunding HST that was never remitted creates an imbalance in Input Tax Credit (ITC) reporting
- At scale, this could constitute a reportable tax compliance issue for the corporation

---

## 8. Recommended Fix

1. **Modify refund logic** to look up and reference tax fields from the original transaction record, not recalculate independently
2. **Enforce a rule:** if original HST on a line item = $0.00, refund HST must also = $0.00
3. **Add a validation check:** total refund amount per line item must never exceed the original amount charged
4. **Audit existing refund transactions** to quantify historical over-refunds
5. **Deploy patched firmware** across all affected Stingray 4 / JS 980 units via coordinated rollout with Jtech/PAX

---

## 9. Documentation

- [x] Photographic evidence — Order #187 (purchase screen, HST $0.00)
- [x] Photographic evidence — Order #250 (refund screen, HST $1.10 incorrectly applied)
- [x] Written bug report (this document)
- [x] Date/time/shift recorded (March 20, 2025, 07:28 PM, Shift 3)
- [x] POS terminal identified (Stingray 4 / JS 980)
- [x] Submitted to Tim Hortons via official website
- [x] Submitted to POS vendor (Jtech/PAX Technology)

---

## 10. Disclosure

This report was prepared and submitted in good faith through responsible disclosure channels. All evidence was provided directly to the affected parties. No exploitation of this bug occurred beyond the incidental discovery during normal shift operations.

---

*Report prepared by Mohit Prajapati — github.com/mohitsec*
