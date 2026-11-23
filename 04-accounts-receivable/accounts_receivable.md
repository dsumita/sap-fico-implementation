# 04 - Accounts Receivable

Configuration and master data steps for SAP Financial Accounting, Accounts Receivable (customer sub-ledger).

**Chart of Accounts:** `CAGL`
**Company codes:** 3000 (USD) and 4000 (EUR)

---

## 4.1 Create Customer Reconciliation Account

**T-code:** `FS00`

### Company Code 3000

| Field | Value |
|---|---|
| G/L Account Type | Balance Sheet Account |
| Account Group | Assets |
| Account Currency | USD |
| Sort Key | `31` |
| Field Status Group | `YB67` (Reconciliation accounts) |

### Company Code 4000

Same as company code 3000, except **Account Currency = EUR**.

> Remember to set **Reconciliation Account for Account Type** to Customers (`D`) on the Control Data tab.

---

## 4.2 Create Revenue G/L Account

**T-code:** `FS00`

### Company Code 3000

| Field | Value |
|---|---|
| G/L Account Type | Sales Revenue |
| Account Group | Revenue |
| Account Currency | USD |
| Sort Key | (blank) |
| Field Status Group | `YB29` |

### Company Code 4000

Same as company code 3000, except **Account Currency = EUR**.

---

## 4.3 Verify Document Splitting Categories and Related Settings

### Classify G/L Accounts for Document Splitting

**Menu path:** SPRO → Financial Accounting → General Ledger Accounting → Business Transactions → Document Splitting → Classify G/L Accounts for Document Splitting

- Verify the customer reconciliation account is assigned to the Customer item category.
- Verify the revenue account is assigned to the Revenue item category.

### Customer Account Group and BP/CVI Mapping

**Menu path:** SPRO → Cross-Application Components → Master Data Synchronization → Customer/Vendor Integration → Business Partner Settings → Settings for Customer Integration → Field Assignment for Customer Integration → Assign Keys → Define Number Assignment for Direction BP to Customer

### Payment Terms

**T-code:** `OBB8`

### Customer Tolerance Group

**T-code:** `OBA3`

---

## 4.4 Create Customer Business Partner

**Status:** ⬜ In progress
**T-code:** `BP`

| Field | Value |
|---|---|
| Name | (customer name) |
| Search Term | Customers |
| BP Role | FI Customer (Fin. Accounting) |
| Company Code | `3000` (repeat for `4000`) |
| Control Tab: Reconciliation Account | Customer reconciliation account from 4.1 |
| Payment Transactions Tab: Payment Terms | Payment terms verified in 4.3 (`OBB8`) |

- Create the customer for both company codes.
- Record the customer number here: `__________`

---

## 4.5 Post Customer Invoice

**Status:** ⬜ Not started
**T-code:** `FB70`

| Field | Value |
|---|---|
| Document Type | `DR` (standard customer invoice) |
| Customer | (customer number from 4.4) |
| Invoice Date / Posting Date | Today |
| Reference | `INV-2026-001` |
| Amount | `500` |
| Calculate Tax | (tax code, e.g. output tax) |

**G/L line item:**

| G/L Account | Short Text | Amount in Doc. Currency |
|---|---|---|
| (revenue account from 4.2) | Sales Revenue | 500 |

Expected entry: debit the customer reconciliation account (via the customer), credit Sales Revenue.

---

## 4.6 Post Incoming Payment

**Status:** ⬜ Not started
**T-code:** `F-28`

| Field | Value |
|---|---|
| Document Date / Posting Date | Today |
| Document Type | `DZ` |
| Company Code | `3000` |
| Currency | USD |
| Bank Account (G/L) | `100000` |
| Amount | `500` |
| Account (Customer) | (customer number from 4.4) |

Expected entry: debit Bank `100000`, credit the customer (clears the receivable).

> The open invoice from 4.5 must be selected and cleared during payment posting.

---

## 4.7 Test for Company Code 4000 (EUR)

**Status:** ⬜ Not started

Repeat 4.4 to 4.6 for company code **4000**, using currency **EUR**.

| Step | Company Code 3000 (USD) | Company Code 4000 (EUR) |
|---|---|---|
| Customer business partner (4.4) | ⬜ | ⬜ |
| Customer invoice, `FB70` (4.5) | ⬜ | ⬜ |
| Incoming payment, `F-28` (4.6) | ⬜ | ⬜ |



---

