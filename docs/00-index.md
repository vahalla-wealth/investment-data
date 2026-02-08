# Vahalla Wealth Management System — Document Index

| | |
|---|---|
| **System** | Vahalla Wealth Management System |
| **Classification** | Confidential |
| **Maintained By** | Vahalla System Team |

---

## Table of Contents

| # | Document | Description | Status |
|---|---|---|---|
| 01 | [Wealth Management Specification](01-wealth-management-specification.md) | Client, Account, Portfolio, Position, Transaction, Order & FX Deposit data fields | Draft |
| 02 | [Bank Data Specification — Equity & Bond](02-bank-data-specification-equity-bond.md) | Data fields, formats, ISO 20022 references, and standards for Equity and Bond asset classes | Draft |
| 02.1 | [↳ CSV Sample Data — Equity & Bond](02.1-sample-data-csv-equity-bond.md) | CSV format examples, formatting rules, header rows and sample data | Draft |
| 02.2 | [↳ Sample Data — Position (CSV & JSON)](02.2-sample-data-csv-position.md) | Position samples mixing Equity and Bond holdings | Draft |
| 02.3 | [↳ Sample Data — Transaction & Order (CSV & JSON)](02.3-sample-data-csv-transaction-order.md) | Transaction and Order samples mixing Equity and Bond trades | Draft |
| 03 | [Bank Data Specification — Investment Funds](03-bank-data-specification-investment-funds.md) | Data fields, formats, ISO 20022 references for Mutual Funds, ETFs, Hedge Funds, REITs, etc. | Draft |
| 03.1 | [↳ Sample Data — Investment Funds (CSV & JSON)](03.1-sample-data-csv-investment-funds.md) | CSV & JSON examples for ETF, Mutual Fund, and Hedge Fund records | Draft |
| 03.2 | [↳ Sample Data — Fund Position, Transaction & Order (CSV & JSON)](03.2-sample-data-csv-fund-position-transaction.md) | Fund Position, Subscription, Redemption, Switch, and Distribution samples | Draft |

---

## Planned Documents

| # | Document | Description | Status |
|---|---|---|---|
| 04 | Bank Data Specification — Derivatives | Data fields for Options, Swaps, Futures, Forwards | Planned |
| 05 | Bank Data Specification — FX & Money Market | Data fields for FX Instruments and Money Market | Planned |
| 06 | Bank Data Specification — Structured Products & Commodities | Data fields for Structured Products and Commodities | Planned |
| 07 | Bank Data Specification — FX Deposit | Data fields for FX Deposit (Account Source Fund) | Planned |

---

## Document Naming Convention

All documents follow the naming pattern:

```
[##]-[document-type]-[asset-class].md
```

- **##** — Sequential number matching the Table of Contents
- **document-type** — e.g. `bank-data-specification`, `sample-data-csv`
- **asset-class** — e.g. `equity-bond`, `derivatives`, `investment-funds`
