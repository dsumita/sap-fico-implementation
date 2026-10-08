# sap-fico-implementation
SAP S/4HANA FICO – End-to-End Implementation Practice Project


A hands-on SAP S/4HANA Financial Accounting (FI) and Controlling (CO) configuration project based on the Global Bike Ltd. business scenario.
Hands-on SAP FI/CO configuration log for a fictional company, **Global Bike Ltd**, with a US company code (USD) and an EU company code (EUR). Each phase folder has its own README with the detailed steps, T-codes, values, screenshots and troubleshooting notes

This repository documents configuration activities, implementation progress, screenshots, and troubleshooting lessons learned while working through SAP FICO business processes.

## Implementation Progress

* [x] [01 — Enterprise Structure](./01-enterprise-structure/)
* [ ] [02 — General Ledger Accounting](./02-general-ledger/)
* [ ] [03 — Accounts Payable](./03-accounts-payable/)
* [ ] [04 — Accounts Receivable](./04-accounts-receivable/)
* [ ] [05 — Asset Accounting](./05-asset-accounting/)
* [ ] [06 — Controlling](./06-controlling/)

*Progress is updated as configuration activities are completed and validated.*

## 🏢 Business Scenario Summary

This project simulates an enterprise financial implementation for **Global Bike Ltd.** across North American and European operational entities on an SAP S/4HANA system.

| Configuration Item | US Operations | European Operations |
| :--- | :--- | :--- |
| **System Environment** | SAP S/4HANA Trial / Sandbox | SAP S/4HANA Trial / Sandbox |
| **Group Company** | Global Bike Ltd (`GBL`) | Global Bike Ltd (`GBL`) |
| **Company Code** | `3000` (Global Bike Ltd US) | `4000` (Global Bike Ltd EU) |
| **Local Currency** | `USD` | `EUR` |
| **Chart of Accounts** | Operating COA (`CAGL`) | Operating COA (`CAGL`) |
| **Fiscal Year Variant** | `K4` (Calendar Year, 12 + 4) | `K4` (Calendar Year, 12 + 4) |
| **Posting Period Variant** | `GBLT` | `GBLT` |
| **Field Status Variant** | `0010` | `0010` |

## Repository Structure

| Directory                  | Description                                   |
| -------------------------- | --------------------------------------------- |
| `01-enterprise-structure/` | Organizational structure configuration        |
| `02-general-ledger/`       | G/L configuration and posting activities      |
| `03-accounts-payable/`     | Supplier accounting and payments              |
| `04-accounts-receivable/`  | Customer accounting and incoming payments     |
| `05-asset-accounting/`     | Fixed asset accounting                        |
| `06-controlling/`          | Controlling configuration and activities      |
| `tracker/`                 | Configuration log and implementation progress |
| `reference/`               | Transaction codes and technical notes         |

## Project Objectives

* Develop practical SAP S/4HANA FI/CO configuration skills.
* Understand organizational dependencies and financial business processes.
* Document configuration decisions and validation results.
* Practice troubleshooting and maintain a structured implementation log.

## Project Status

**In Progress**

## Disclaimer

This is an independent learning and implementation practice project based on a simulated business scenario. It does not represent a production implementation for Global Bike Ltd. or work performed for an actual client.

