# 02 - General Ledger Accounting

Configuration and master data steps for SAP Financial Accounting, General Ledger Accounting.

**Chart of Accounts:** `CAGL`

---

## 2.1 Define G/L Account Groups

**T-code:** `OBD4`
**Menu path:** Financial Accounting → General Ledger Accounting → Master Data → G/L Accounts → Preparations → Define Account Group

| Account Group | Description | Number Range |
|---|---|---|
| ASST | Assets | 100000 – 199999 |
| LIAB | Liabilities | 200000 – 299999 |
| EQTY | Equity | 300000 – 399999 |
| REVN | Revenue | 400000 – 499999 |
| EXPN | Expenses | 500000 – 599999 |

---

## 2.2 Define Retained Earnings Account

**T-code:** `OB53`

| Field | Value |
|---|---|
| P&L Statement Account Type | `X` |
| Retained Earnings Account | `330000` |

- The Retained Earnings Account represents the cumulative net income or loss carried forward from previous fiscal years.
- Because it is part of Stockholders' Equity, it should reside in the Equity account range (300000 – 399999).
- Use account type `X` when you only need one retained earnings account for the whole chart of accounts.

---

## 2.3 Define Document Types & Number Ranges

**T-codes:** `OBA7` (document types), `FBN1` (number ranges)

- Number ranges were auto-created when company codes 3000 and 4000 were created, even though no G/L data was copied.

---

## 2.4 Taxes

### 2.4.1 Assign Tax Procedure

**T-code:** `OBBG`

### 2.4.2 Create Tax Codes

**T-code:** `FTXP`
**Menu path:** Financial Accounting → Financial Accounting Global Settings → Tax on Sales/Purchases → Calculation → Define Tax Codes for Sales and Purchases

| Country | Tax Procedure |
|---|---|
| US | TAXUS |
| DE | TAXD |

> ⚠️ Avoided `TAXUSJ` to skip the complexity of jurisdiction codes.

---

## 2.5 Create G/L Master Records

**T-code:** `FS00`
**Menu path (SAP Easy Access):** Accounting → Financial Accounting → General Ledger → Master Records → G/L Accounts → Individual Processing → Centrally

- Created centrally (chart of accounts level and company code level together).
- For details, see the **G/L Master Data & Configuration Summary** doc file.

---

## 2.6 Post Manual G/L Entries

**T-code:** `FB50` (Enter G/L Account Document)
**Company codes:** 3000 and 4000 (post separately in each)

| Line | G/L Account | Description | Amount | Debit / Credit |
|---|---|---|---|---|
| 1 | 500000 | Office Expense | 500 | Debit |
| 2 | 100000 | Asset account (e.g., Cash/Bank) | 500 | Credit |

Total debits equal total credits (500 = 500), so the document balances

### Error Encountered

The posting gave an error and required extra steps:

- **Classify G/L Accounts for Document Splitting**

### Reverse Document

**T-code:** `FB08`

- Use when a document was posted to the wrong account or with the wrong amount.
- Works like a delete: the original is kept and an offsetting reversal document is created.

### Accrual

- To be documented.

---

