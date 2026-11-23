# 03 - Accounts Payable

Configuration and transaction steps for SAP Financial Accounting, Accounts Payable (vendor sub-ledger).

**Chart of Accounts:** `CAGL`
**Company codes:** 3000 (USD) and 4000 (EUR)

---

## 3.1 Create Reconciliation G/L Account

**T-code:** `FS00`
**Menu path:** Accounting → Financial Accounting → General Ledger → Master Records → G/L Accounts → Individual Processing → Centrally

Create for company code **3000**, then repeat for **4000** (USD and EUR company codes).

| Tab | Field | Value | Notes |
|---|---|---|---|
| Header | G/L Account | `290000` | Must fall within the Liabilities range (200000 – 299999) |
| Header | Company Code | `3000` (repeat for `4000`) | Create for both USD and EUR company codes |
| Type/Name | Account Group | `LIAB` | Liabilities group |
| Type/Name | P&L / Balance Sheet | Balance Sheet Account | Reconciles sub-ledger balances |
| Type/Name | Short Text / Long Text | Trade Payables | Descriptive name |
| Control Data | Reconciliation Account for Acc. Type | Vendors (`K`) | Tells SAP this is a sub-ledger reconciliation account |
| Control Data | Sort Key | `1` | Posting date sort key |
| Create/Bank/Interest | Field Status Group | `YB11` | Standard reconciliation account group |

---

## 3.2 Verify Document Splitting Classification

**T-code:** `GSP_LZ2`

- Purpose: make sure the liability account range (including `290000`) is assigned to item category `03000` (Vendor).
- No change needed if the range is already assigned.

---

## 3.3 Create Vendor Master Data

**T-code:** `BP`

| Field | Value |
|---|---|
| Name | Global Supplies |
| Search Term | Suppliers |
| BP Role | Supplier (Fin. Accounting) |
| Control Tab: Reconciliation Account | `290000` |
| Payment Transactions Tab: Payment Terms | `0001` |

- Created for both company code **3000** and **4000**.
- Vendor number used in testing: `3000061`.

---

## 3.4 Post a Test Vendor Invoice

**T-code:** `FB60`

| Field | Value | Notes |
|---|---|---|
| Document Type | `KR` | Standard SAP document type for a vendor invoice |
| Supplier | `3000061` | |
| Reference | `INV-2026-001` | |
| Amount | `500` | |
| Calculate Tax | `I0` | Tax code |

**G/L line item:**

| G/L Account | Short Text | Amount in Doc. Currency |
|---|---|---|
| 500000 | Expense | 500 |

Resulting entry: debit Expense `500000`, credit Trade Payables `290000` (via vendor `3000061`).

---

## 3.5 Post Outgoing Payment

**T-code:** `F-53`
**Menu path:** Accounting → Financial Accounting → Accounts Payable → Outgoing Payment → Post

| Field | Value |
|---|---|
| Document Date / Posting Date | Today |
| Document Type | `KZ` |
| Company Code | `3000` |
| Currency | USD |
| Bank Account (G/L) | `100000` |
| Amount | `500` |
| Account (Vendor) | `3000061` |

Resulting entry: debit vendor (clears the payable), credit Bank `100000`.

> The open invoice from 3.4 must be selected and cleared during payment posting.

---

## 3.6 Company Code 4000 (EUR) Testing

The invoice and payment tests from 3.4 and 3.5 were also completed for company code **4000**.

| Step | Company Code 3000 | Company Code 4000 |
|---|---|---|
| Currency | USD | EUR |
| Reconciliation account | `290000` | `290000` |
| Vendor invoice (`FB60`, type `KR`) | Posted | Posted |
| Outgoing payment (`F-53`, type `KZ`) | Posted | Posted |

