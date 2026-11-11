# Global Bike Group: SAP S/4HANA Multi-Currency FICO Implementation

## Enterprise Structure

A hands-on SAP S/4HANA Finance (FI) build for a fictional bicycle company operating in the US and the EU. This document covers the enterprise structure and the global financial settings that sit underneath it.

**Environment:** SAP S/4HANA (on-premise, sandbox client) **Scope:** Company, company codes, chart of accounts, fiscal year and posting period settings, field status, tolerances, G/L account groups, tax procedures **Reporting base currency:** USD

---

## 1\. Status Summary

| Area | Status |
| :---- | :---- |
| Company and company codes | Done |
| Chart of accounts and assignment | Done |
| Fiscal year variant and posting periods | Done |
| Field status variant | Done |
| G/L tolerance groups | Done |
| Employee tolerance groups and user assignment | Pending verification |
| G/L account groups and retained earnings | Done |
| Tax procedures | Done |
| Document splitting classification | In progress |
| Controlling area | Planned, not yet configured |
| Group currency and currency translation | Deferred |

---

## 2\. Structure Diagram

```
flowchart TD
    CLIENT["Client"] --> COMPANY["Company GBL<br/>Global Bike Ltd"]
    COMPANY --> CC3000["Company Code 3000<br/>Global Bike Ltd US<br/>USD"]
    COMPANY --> CC4000["Company Code 4000<br/>Global Bike Ltd EU<br/>EUR"]
    CC3000 --> COA["Chart of Accounts CAGL<br/>6-digit"]
    CC4000 --> COA
    CLIENT -.-> CO["Controlling Area C001<br/>(planned)"]
    CO -.-> CC3000
    CO -.-> CC4000
```

Solid lines are configured. Dashed lines are planned.

---

## 3\. Organizational Objects

| Object | Key | Description | Currency |
| :---- | :---- | :---- | :---- |
| Company | `GBL` | Global Bike Ltd, the legal and consolidation grouping | USD (reporting base) |
| Company code | `3000` | Global Bike Ltd US | USD |
| Company code | `4000` | Global Bike Ltd EU (Germany) | EUR |
| Chart of accounts | `CAGL` | Global Bike Operating COA, English, 6-digit account numbers | n/a |
| Fiscal year variant | `K4` | Calendar year, 12 posting periods \+ 4 special periods | n/a |
| Posting period variant | `GBLT` | Global Bike posting periods, shared by both company codes | n/a |
| Field status variant | `0010` | Shared by both company codes | n/a |
| Controlling area | `C001` | Planned, cross-company cost accounting | USD (planned) |

---

## 4\. Design Decisions

| Decision | Choice | Rationale |
| :---- | :---- | :---- |
| Company codes per company | Two (US and EU) under one company | Models a multi-currency group with a single consolidation parent |
| Chart of accounts | One shared chart (`CAGL`) | No statutory need for a separate chart per country; simpler to maintain |
| Account number length | 6 digits | Easier to read and to range into account groups |
| Field status variant | One shared variant (`0010`) | Field requirements are an internal business choice, not a country-specific rule |
| Posting period variant | One shared variant (`GBLT`) | US and EU close on the same schedule |
| Fiscal year variant | `K4` (calendar year) | Standard SAP-delivered variant; both company codes must share a compatible variant to share a controlling area |
| Company codes created clean | G/L data and controlling assignment not copied | Avoids carrying reference data from the template company code (see Lessons Learned) |
| Reporting currency | USD | US-based group |
| Tax procedure for US | `TAXUS` instead of `TAXUSJ` | Avoids tax jurisdiction code maintenance, which is out of scope |
| Operating concern (CO-PA) | Deferred | Cost accounting should be stable before adding profitability analysis |

---

## 5\. Configuration Log

All paths are under the IMG (`SPRO`) unless noted.

| Step | Activity | T-Code | IMG Path | Values |
| :---- | :---- | :---- | :---- | :---- |
| 1.1 | Define company | `OX15` | Enterprise Structure → Definition → Financial Accounting → Define Company | `GBL`, Global Bike Ltd |
| 1.2 | Create company codes (copy method) | `EC01` / `OX02` | Enterprise Structure → Definition → Financial Accounting → Edit, Copy, Delete, Check Company Code | `3000`, `4000`. G/L data not copied. Controlling assignment not copied. |
| 1.3 | Edit company code data | `OX02` | Same as above | `3000` Global Bike Ltd US (USD). `4000` Global Bike Ltd EU (EUR). |
| 1.4 | Assign company codes to company | `OX16` | Enterprise Structure → Assignment → Financial Accounting → Assign Company Code to Company | `3000` → `GBL`, `4000` → `GBL` |
| 1.5 | Define chart of accounts | `OB13` | Financial Accounting → General Ledger Accounting → Master Data → G/L Accounts → Preparations → Edit Chart of Accounts List | `CAGL`, English, 6 digits |
| 1.5a | Assign chart of accounts | `OBY6` | Same branch → Assign Company Code to Chart of Accounts | `3000` → `CAGL`, `4000` → `CAGL` |
| 1.5b | Assign field status variant | `OBC5` | Financial Accounting → Financial Accounting Global Settings → Ledgers → Fields → Assign Company Code to Field Status Variants | `3000` → `0010`, `4000` → `0010` |
| 1.6 | Fiscal year variant (verify delivered) | `OB29` | Financial Accounting Global Settings → Ledgers → Fiscal Year and Posting Periods → Maintain Fiscal Year Variant | `K4` used as delivered |
| 1.6a | Assign fiscal year variant | `OB37` | Same branch → Assign Company Code to a Fiscal Year Variant | `3000` → `K4`, `4000` → `K4` |
| 1.7 | Define posting period variant | `OBBO` | Same branch → Posting Periods → Define Variants for Open Posting Periods | `GBLT` |
| 1.7a | Assign posting period variant | `OBBP` | Same branch → Assign Variants to Company Code | `GBLT` → `3000`, `4000` |
| 1.7b | Open and close posting periods | `OB52` | Same branch → Open and Close Posting Periods | Account type `+`, periods 1-12 and special periods 13-16, FY2026 |
| 1.8 | G/L account tolerance groups | `OBA0` | General Ledger Accounting → Business Transactions → Open Item Clearing → Clearing Differences → Define Tolerance Groups for G/L Accounts | Auto-clear differences up to \$100 or 2% |
| 1.8a | Employee tolerance groups | `OBA4` | Financial Accounting Global Settings → Document → Tolerance Groups → Define Tolerance Groups for Employees | Pending verification |
| 1.8b | Assign users to tolerance groups | `OB57` | Financial Accounting Global Settings → Document → Line Item → Assign User/Tolerance Groups | Pending verification |

### G/L Foundation

| Step | Activity | T-Code | Values |
| :---- | :---- | :---- | :---- |
| 2.1 | Define G/L account groups | `OBD4` | See table below |
| 2.2 | Define retained earnings account | `OB53` | P\&L statement account type `X`, account `330000` |
| 2.3 | Document types and number ranges | `OBA7` / `FBN1` | Number ranges were auto-created for both company codes during company code creation |
| 2.4 | Assign country to tax procedure | `OBBG` | `US` → `TAXUS`, `DE` → `TAXD` |
| 2.5 | Classify G/L accounts for document splitting | `GSP_LZG` | Required in S/4HANA before any posting |

**Account groups (chart of accounts `CAGL`):**

| Group | Name | From | To |
| :---- | :---- | :---- | :---- |
| `ASST` | Assets | 100000 | 199999 |
| `LIAB` | Liabilities | 200000 | 299999 |
| `EQTY` | Equity | 300000 | 399999 |
| `REVN` | Revenue | 400000 | 499999 |
| `EXPN` | Expenses | 500000 | 599999 |

---

## 6\. Lessons Learned

**Copying a company code carries more than the name.** The first attempt (company codes 1000 and 2000\) copied G/L account company-code data from the reference company code. Reassigning a different chart of accounts then failed with message `FC140`, which requires resetting company-code data first. The rebuild on 3000 and 4000 answered No to the G/L data copy and avoided the problem. Number range intervals were still created automatically, because they are part of the organizational object rather than G/L data.

**Retained earnings must exist as a real account.** `OB53` only designates an account number. The account itself must be created in `FS00` before any P\&L account can be saved.

**Use the Create button in `FS00`.** Pressing Continue on a new account number tries to open an existing account and fails with an "account does not exist" error.

**Account numbers must fit their account group range.** A GR/IR account numbered 191100 sits inside the Assets range (100000-199999) even though it is a liability. It was renumbered into the Liabilities range.

**S/4HANA requires document splitting classification.** Every G/L account needs an item category before the first posting, or the posting is rejected.

**A warning is not always ignorable.** The `OB53` warning about a missing account looked informational but became a hard block later in `FS00`.

**Company currency and company code currency may differ.** The warning shown when assigning company code 4000 (EUR) to a company with a USD currency is expected for a multi-currency group. It is resolved later through group currency and translation settings.

---

## 7\. Open Items and Next Steps

1. Create and assign the controlling area `C001` (`OKKP`) with cross-company cost accounting for both company codes  
2. Verify and document employee tolerance groups (`OBA4`, `OB57`)  
3. Configure group currency, parallel currency activation (`OB22`), and exchange rates (`OB08`)  
4. Complete document splitting classification and post test entries in both company codes (`F-02`)  
5. Run month-end tests: foreign currency valuation (`F.05`) and draft financial statements (`F.01`)  
6. Proceed to Accounts Payable and Accounts Receivable using Business Partner (`BP`) master data, which replaces vendor and customer master transactions in S/4HANA