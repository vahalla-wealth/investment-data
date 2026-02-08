# Vahalla Wealth Management — Investment Data

> Data specifications, GraphQL schemas, and sample data for the Vahalla Wealth Management System.

## Overview

This repository contains the **data model definitions** and **specification documents** for the Vahalla Wealth Management platform. It covers financial instrument data across multiple asset classes, aligned with **ISO 20022** messaging standards.

### Asset Classes (ISO 20022)

| Asset Class | Schema | Specification | Status |
|---|---|---|---|
| **Securities** (Equity & Bond) | `securities.graphqls` | [02 — Equity & Bond](docs/02-bank-data-specification-equity-bond.md) | Draft |
| **Investment Funds** (ETF, Mutual Fund, Hedge Fund) | `investment-funds.graphqls` | [03 — Investment Funds](docs/03-bank-data-specification-investment-funds.md) | Draft |
| **Derivatives** (Options, Swaps, Futures) | `derivatives.graphqls` | — | Planned |
| **FX & Money Market** | `foreign-exchange.graphqls`, `money-market.graphqls` | — | Planned |
| **Structured Products & Commodities** | `structured-products.graphqls`, `commodities.graphqls` | — | Planned |

### Wealth Management Domain

| Domain | Schema | Specification | Status |
|---|---|---|---|
| Client, Account, Portfolio, Position, Transaction, Order, FX Deposit | `wealth-management.graphqls` | [01 — Wealth Management](docs/01-wealth-management-specification.md) | Draft |

---

## Repository Structure

```
investment-data/
├── README.md
├── docs/                          # Specification & sample data documents
│   ├── 00-index.md                # Document index & table of contents
│   ├── 01-wealth-management-specification.md
│   ├── 02-bank-data-specification-equity-bond.md
│   ├── 02.1-sample-data-csv-equity-bond.md
│   ├── 02.2-sample-data-csv-position.md
│   ├── 02.3-sample-data-csv-transaction-order.md
│   ├── 03-bank-data-specification-investment-funds.md
│   ├── 03.1-sample-data-csv-investment-funds.md
│   └── 03.2-sample-data-csv-fund-position-transaction.md
└── schemas/                       # GraphQL schema definitions
    ├── base.graphqls              # Scalars, enums, interfaces (AssetClass, SecurityType, etc.)
    ├── securities.graphqls        # Equity & Bond types
    ├── investment-funds.graphqls  # Investment Fund types (FundSecurityType, InvestmentFundType, etc.)
    ├── derivatives.graphqls       # Derivatives types
    ├── foreign-exchange.graphqls  # FX instrument types
    ├── money-market.graphqls      # Money market types
    ├── structured-products.graphqls
    ├── commodities.graphqls
    ├── wealth-management.graphqls # Client, Account, Portfolio, Position, Transaction, Order
    └── queries.graphqls           # GraphQL query definitions
```

---

## Documents

Full document index: [`docs/00-index.md`](docs/00-index.md)

### Specifications

Each specification document defines data fields, types, validation rules, and ISO 20022 message references for a given asset class.

| # | Document | Description |
|---|---|---|
| 01 | [Wealth Management Specification](docs/01-wealth-management-specification.md) | Client, Account, Portfolio, Position, Transaction, Order & FX Deposit |
| 02 | [Equity & Bond Specification](docs/02-bank-data-specification-equity-bond.md) | Data fields, formats, ISO 20022 references for Equity and Bond |
| 03 | [Investment Funds Specification](docs/03-bank-data-specification-investment-funds.md) | Data fields for Mutual Funds, ETFs, Hedge Funds, REITs |

### Sample Data (CSV & JSON)

Each sample data document provides header rows, data rows, multi-row file examples, and JSON examples with formatting rules.

| # | Document | Covers |
|---|---|---|
| 02.1 | [Equity & Bond — Master Data](docs/02.1-sample-data-csv-equity-bond.md) | Equity and Bond instrument records |
| 02.2 | [Position](docs/02.2-sample-data-csv-position.md) | Equity and Bond position holdings |
| 02.3 | [Transaction & Order](docs/02.3-sample-data-csv-transaction-order.md) | Buy/Sell transactions and orders |
| 03.1 | [Investment Funds — Master Data](docs/03.1-sample-data-csv-investment-funds.md) | ETF, Mutual Fund, Hedge Fund records |
| 03.2 | [Fund Position, Transaction & Order](docs/03.2-sample-data-csv-fund-position-transaction.md) | Fund positions, subscriptions, redemptions, switches, distributions |

---

## GraphQL Schemas

The schema layer defines the data model using GraphQL type definitions. All schemas share common types from `base.graphqls`.

### Classification Hierarchy

```
AssetClass (broad)
├── SECURITIES
│   └── SecurityType: EQUITY, BOND, NOTE, DEBENTURE, PREFERRED_STOCK, CONVERTIBLE_BOND, WARRANT
├── INVESTMENT_FUNDS
│   ├── FundSecurityType: ETF, OPEN_END_INVESTMENT_FUND, CLOSED_END_INVESTMENT_FUND, HEDGE_FUND, ...
│   └── InvestmentFundType: ETF, MUTUAL_FUND, HEDGE_FUND, REIT, INDEX_FUND, ...
├── DERIVATIVES
├── MONEY_MARKET_INSTRUMENTS
├── FOREIGN_EXCHANGE_INSTRUMENTS
├── STRUCTURED_HYBRID_PRODUCTS
└── COMMODITIES
```

### Key Interfaces

- **`Asset`** — Common identification fields shared by all asset types (id, ISIN, CUSIP, SEDOL, ticker, name, assetClass, currency, LEI, FISN, CFI)
- **`Security`** — Extends Asset with exchange-traded security fields (securityType, MIC, tradingStatus, settlementCycle)

---

## Standards & References

| Standard | Usage |
|---|---|
| **ISO 20022** | Field mappings to financial messaging standard (reda, setr, semt, sese message types) |
| **ISO 6166** | ISIN — International Securities Identification Number |
| **ISO 6167** | CUSIP — Committee on Uniform Securities Identification Procedures |
| **ISO 6168** | SEDOL — Stock Exchange Daily Official List |
| **ISO 4217** | Currency codes (USD, EUR, GBP, etc.) |
| **ISO 10383** | MIC — Market Identifier Code |
| **ISO 17442** | LEI — Legal Entity Identifier |
| **ISO 9362** | BIC — Business Identifier Code |
| **ISO 10962** | CFI — Classification of Financial Instruments |

---

## Classification

**Confidential** — Vahalla Wealth Management System

Maintained by **Vahalla System Team**
