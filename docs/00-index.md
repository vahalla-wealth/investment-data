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
| 04 | [Bank Data Specification — Money Market Instruments](04-bank-data-specification-money-market.md) | Data fields, formats, ISO 20022 references for T-Bills, CP, CDs, Repos, Term Deposits | Draft |
| 04.1 | [↳ Sample Data — Money Market Instruments (CSV & JSON)](04.1-sample-data-csv-money-market.md) | CSV & JSON examples for Treasury Bill, Commercial Paper, CD, Repo, Term Deposit | Draft |
| 05 | [Bank Data Specification — Derivatives](05-bank-data-specification-derivatives.md) | Data fields, formats, ISO 20022 references for Options, Swaps, Futures, Forwards | Draft |
| 05.1 | [↳ Sample Data — Derivatives (CSV & JSON)](05.1-sample-data-csv-derivatives.md) | CSV & JSON examples for Option, Swap, Future, Forward, Lifecycle, Underlying, Risk Metrics | Draft |
| 06 | [Bank Data Specification — Foreign Exchange Instruments](06-bank-data-specification-foreign-exchange.md) | Data fields, formats, ISO 20022 references for FX Spot, Forward, Swap, NDF, Options, Cross-Currency Swaps | Draft |
| 06.1 | [↳ Sample Data — Foreign Exchange (CSV & JSON)](06.1-sample-data-csv-foreign-exchange.md) | CSV & JSON examples for FX Trade, Market Data, Forward Curve, Settlement, Cashflow, Lifecycle, Fixing | Draft |
| 07 | [Bank Data Specification — Structured Products](07-bank-data-specification-structured-products.md) | Data fields, formats, ISO 20022 references for Structured Products (autocallables, reverse convertibles, capital-protected notes, etc.) | Draft |
| 07.1 | [↳ Sample Data — Structured Products (CSV & JSON)](07.1-sample-data-csv-structured-products.md) | CSV & JSON examples for Structured Product, Coupon Schedule, Barrier Observation, Autocall Scenario | Draft |
| 07.2 | [Bank Data Specification — Commodities](07.2-bank-data-specification-commodities.md) | Data fields, formats, ISO 20022 references for Precious Metals, Base Metals, Energy, Agricultural, Livestock commodities | Draft |
| 07.2.1 | [↳ Sample Data — Commodities (CSV & JSON)](07.2.1-sample-data-csv-commodities.md) | CSV & JSON examples for Commodity Master, Price, Delivery, Storage, Quality, Forward Curve, Inventory, Spread | Draft |

---

## Planned Documents

| # | Document | Description | Status |
|---|---|---|---|
| 08 | Bank Data Specification — FX Deposit | Data fields for FX Deposit (Account Source Fund) | Planned |

---

## Document Naming Convention

All documents follow the naming pattern:

```
[##]-[document-type]-[asset-class].md
```

- **##** — Sequential number matching the Table of Contents
- **document-type** — e.g. `bank-data-specification`, `sample-data-csv`
- **asset-class** — e.g. `equity-bond`, `derivatives`, `investment-funds`
