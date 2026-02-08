# 01 — Vahalla Wealth Management Data Specification

> **Document Index:** [00-index.md](00-index.md) | **Related:** [02 — Equity & Bond Specification](02-bank-data-specification-equity-bond.md)

| | |
|---|---|
| **Document Title** | Wealth Management Module — Client, Account, Portfolio, Position, Transaction, Order & FX Deposit |
| **System** | Vahalla Wealth Management System |
| **Document Version** | 1.0 |
| **Date** | 2026-02-09 |
| **Classification** | Confidential |
| **Status** | Draft |
| **Prepared By** | Vahalla System Team |

### Revision History

| Version | Date | Author | Description |
|---|---|---|---|
| 1.0 | 2026-02-09 | Vahalla System Team | Initial draft — Wealth Management domain specification |

---

## 1. Purpose

This document defines the data fields, formats, and standards for the **Wealth Management domain** of the Vahalla Investment Module. It covers the core operational entities that manage client relationships, accounts, portfolios, positions, transactions, orders, and FX deposits.

This specification is aligned with **ISO 20022** messaging standards and is intended for banking partners who provide data feeds for the wealth management layer.

For security master data (Equity & Bond fields), refer to [02 — Bank Data Specification — Equity & Bond](02-bank-data-specification-equity-bond.md).

---

## 2. Entity Relationship Overview

```mermaid
graph TD
    RelationshipManager -->|manages| Client
    Client -->|owns| Account
    Account -->|contains| Portfolio
    Account -->|holds| Position
    Account -->|records| Transaction
    Account -->|places| Order
    Account -->|source fund| FXDeposit
    Portfolio -->|tracks| Position
    Order -->|generates| Transaction
    Position -->|references| Asset
```

---

## 3. Data Standards & Conventions

### 3.1 Requirement Levels

| Label | Description |
|---|---|
| Required | Must be provided in every record |
| Conditional | Required when a specific condition is met |
| Optional | Provide if available |

### 3.2 Data Types

| Type | Format | Example |
|---|---|---|
| String | UTF-8 text | `"John Smith"` |
| Int | Integer | `42` |
| Decimal | Decimal number (no thousands separator) | `1855000.00` |
| Boolean | `true` / `false` | `true` |
| Date | ISO 8601 `YYYY-MM-DD` | `"2026-02-08"` |
| DateTime | ISO 8601 `YYYY-MM-DDTHH:MM:SSZ` | `"2026-02-08T16:00:00Z"` |
| Enum | Predefined value from reference table | `"ACTIVE"` |
| ID | Unique identifier string | `"CLI-001234"` |

### 3.3 ISO Standards

| Standard | Usage |
|---|---|
| ISO 4217 | Currency codes (3 chars) |
| ISO 3166 | Country codes (alpha-2) |
| ISO 8601 | Date and time formats |
| ISO 17442 | Legal Entity Identifier (LEI, 20 chars) |
| ISO 9362 | Bank Identifier Code (BIC, 8 or 11 chars) |
| ISO 10383 | Market Identifier Code (MIC, 4 chars) |
| ISO 20022 | Financial messaging standard |

---

## 4. Client Data Fields

### 4.1 Client Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique client identifier | `"CLI-001234"` |
| 2 | `clientType` | Enum | Required | Client classification | See [Appendix A.1](#a1-clienttype) |
| 3 | `status` | Enum | Required | Client status | See [Appendix A.2](#a2-clientstatus) |
| 4 | `lei` | String | Optional | Legal Entity Identifier per ISO 17442 (corporate clients) | `"HWUPKR0MPOU8FGXBT394"` |
| 5 | `bic` | String | Optional | Bank Identifier Code per ISO 9362 | `"BOFAUS3NXXX"` |
| 6 | `nationalId` | String | Optional | National identification number | `"123-45-6789"` |
| 7 | `taxId` | String | Optional | Tax identification number | `"91-1234567"` |

### 4.2 Client Name & Contact

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 8 | `firstName` | String | Optional | First name (individual clients) | `"John"` |
| 9 | `lastName` | String | Optional | Last name (individual clients) | `"Smith"` |
| 10 | `fullName` | String | Required | Full legal name | `"John Smith"` |
| 11 | `dateOfBirth` | Date | Optional | Date of birth | `"1985-06-15"` |
| 12 | `nationality` | String | Optional | Nationality per ISO 3166 alpha-2 | `"US"` |
| 13 | `countryOfResidence` | String | Optional | Country of residence per ISO 3166 | `"US"` |
| 14 | `language` | String | Optional | Preferred language per ISO 639 | `"en"` |

### 4.3 Client Address

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 15 | `addressLine1` | String | Optional | Primary address line | `"123 Wall Street"` |
| 16 | `addressLine2` | String | Optional | Secondary address line | `"Suite 4500"` |
| 17 | `city` | String | Optional | City | `"New York"` |
| 18 | `postalCode` | String | Optional | Postal/ZIP code | `"10005"` |
| 19 | `country` | String | Optional | Country per ISO 3166 alpha-2 | `"US"` |
| 20 | `email` | String | Optional | Email address | `"john.smith@email.com"` |
| 21 | `phone` | String | Optional | Phone number | `"+1-212-555-0100"` |

### 4.4 Client KYC & Compliance

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 22 | `kycStatus` | Enum | Optional | KYC verification status | See [Appendix A.3](#a3-kycstatus) |
| 23 | `kycVerificationDate` | DateTime | Optional | Date of last KYC verification | `"2025-06-01T00:00:00Z"` |
| 24 | `kycExpiryDate` | DateTime | Optional | KYC expiry date | `"2026-06-01T00:00:00Z"` |
| 25 | `riskProfile` | Enum | Optional | Client risk profile | See [Appendix A.4](#a4-clientriskprofile) |
| 26 | `fatcaStatus` | String | Optional | FATCA classification | `"COMPLIANT"` |
| 27 | `crsReportable` | Boolean | Optional | CRS reportable flag | `true` |
| 28 | `pepStatus` | Boolean | Optional | Politically Exposed Person flag | `false` |
| 29 | `sanctionsScreeningDate` | DateTime | Optional | Last sanctions screening date | `"2025-12-01T00:00:00Z"` |

### 4.5 Client Classification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 30 | `investorClassification` | String | Optional | Investor classification | `"PROFESSIONAL"` |
| 31 | `mifidClassification` | String | Optional | MiFID II classification | `"RETAIL"` |

### 4.6 Client Relationships & Metadata

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 32 | `relationshipManagerId` | ID | Required | Assigned RM identifier | `"RM-001"` |
| 33 | `onboardingDate` | DateTime | Optional | Client onboarding date | `"2024-01-15T00:00:00Z"` |
| 34 | `lastReviewDate` | DateTime | Optional | Last periodic review date | `"2025-06-01T00:00:00Z"` |
| 35 | `nextReviewDate` | DateTime | Optional | Next scheduled review date | `"2026-06-01T00:00:00Z"` |
| 36 | `createdAt` | DateTime | Required | Record creation timestamp | `"2024-01-15T09:00:00Z"` |
| 37 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T14:00:00Z"` |

---

## 5. Relationship Manager Data Fields

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique RM identifier | `"RM-001"` |
| 2 | `employeeId` | String | Required | Employee ID | `"EMP-5678"` |
| 3 | `firstName` | String | Required | First name | `"Sarah"` |
| 4 | `lastName` | String | Required | Last name | `"Johnson"` |
| 5 | `fullName` | String | Required | Full name | `"Sarah Johnson"` |
| 6 | `email` | String | Required | Email address | `"sarah.johnson@bank.com"` |
| 7 | `phone` | String | Optional | Phone number | `"+1-212-555-0200"` |
| 8 | `title` | String | Optional | Job title | `"Senior Relationship Manager"` |
| 9 | `department` | String | Optional | Department | `"Private Banking"` |
| 10 | `branch` | String | Optional | Branch location | `"New York"` |
| 11 | `region` | String | Optional | Region | `"North America"` |
| 12 | `supervisorId` | ID | Optional | Supervisor RM identifier | `"RM-000"` |
| 13 | `licenseNumber` | String | Optional | License number | `"LIC-2024-001"` |
| 14 | `licenseType` | String | Optional | License type | `"Series 7"` |
| 15 | `licenseExpiryDate` | DateTime | Optional | License expiry date | `"2027-12-31T00:00:00Z"` |
| 16 | `certifications` | [String] | Optional | List of certifications | `["CFA", "CFP"]` |
| 17 | `totalClientsManaged` | Int | Optional | Total clients managed | `45` |
| 18 | `totalAum` | Decimal | Optional | Total assets under management | `250000000.00` |
| 19 | `aumCurrency` | String | Optional | AUM currency per ISO 4217 | `"USD"` |
| 20 | `active` | Boolean | Required | Active status | `true` |
| 21 | `startDate` | DateTime | Optional | Employment start date | `"2020-03-01T00:00:00Z"` |
| 22 | `createdAt` | DateTime | Required | Record creation timestamp | `"2020-03-01T09:00:00Z"` |
| 23 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T14:00:00Z"` |

---

## 6. Account Data Fields

### 6.1 Account Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique account identifier | `"ACC-001234"` |
| 2 | `accountNumber` | String | Required | Account number | `"1234567890"` |
| 3 | `accountName` | String | Optional | Account name | `"John Smith - Investment"` |
| 4 | `accountType` | Enum | Required | Account type | See [Appendix A.5](#a5-accounttype) |
| 5 | `status` | Enum | Required | Account status | See [Appendix A.6](#a6-accountstatus) |

### 6.2 Account Details

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 6 | `baseCurrency` | String | Required | Base currency per ISO 4217 | `"USD"` |
| 7 | `openingDate` | DateTime | Required | Account opening date | `"2024-01-15T00:00:00Z"` |
| 8 | `closingDate` | DateTime | Optional | Account closing date | `null` |
| 9 | `lastActivityDate` | DateTime | Optional | Last activity date | `"2026-02-08T16:00:00Z"` |

### 6.3 Account Servicing

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 10 | `custodian` | String | Optional | Custodian institution name | `"State Street"` |
| 11 | `custodianLei` | String | Optional | Custodian LEI per ISO 17442 | `"549300ZFEEJ2IP5VME73"` |
| 12 | `custodianBic` | String | Optional | Custodian BIC per ISO 9362 | `"SBOSUS33XXX"` |
| 13 | `servicingInstitution` | String | Optional | Servicing institution name | `"Bank of America"` |
| 14 | `servicingInstitutionLei` | String | Optional | Servicing institution LEI | `"9DJT3UXIJIZJI4WXO774"` |

### 6.4 Account Balance

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 15 | `cashBalance` | Decimal | Optional | Cash balance | `150000.00` |
| 16 | `cashCurrency` | String | Optional | Cash currency per ISO 4217 | `"USD"` |
| 17 | `marketValue` | Decimal | Optional | Total market value of holdings | `1855000.00` |
| 18 | `totalValue` | Decimal | Optional | Total value (cash + market value) | `2005000.00` |
| 19 | `valuationDate` | DateTime | Optional | Valuation date | `"2026-02-08T16:00:00Z"` |
| 20 | `valuationCurrency` | String | Optional | Valuation currency per ISO 4217 | `"USD"` |

### 6.5 Account Margin

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 21 | `marginLimit` | Decimal | Optional | Margin limit | `500000.00` |
| 22 | `marginUsed` | Decimal | Optional | Margin used | `150000.00` |
| 23 | `marginAvailable` | Decimal | Optional | Margin available | `350000.00` |
| 24 | `marginCurrency` | String | Optional | Margin currency per ISO 4217 | `"USD"` |

### 6.6 Account Restrictions

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 25 | `tradingRestricted` | Boolean | Optional | Trading restriction flag | `false` |
| 26 | `withdrawalRestricted` | Boolean | Optional | Withdrawal restriction flag | `false` |
| 27 | `restrictionReason` | String | Optional | Reason for restriction | `null` |

### 6.7 Account Tax

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 28 | `taxStatus` | Enum | Optional | Tax status | See [Appendix A.15](#a15-taxstatus) |
| 29 | `taxCountry` | String | Optional | Tax country per ISO 3166 | `"US"` |
| 30 | `withholdingTaxRate` | Decimal | Optional | Withholding tax rate (%) | `30.00` |

### 6.8 Account Settlement

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 31 | `defaultSettlementCurrency` | String | Optional | Default settlement currency per ISO 4217 | `"USD"` |
| 32 | `defaultSettlementCycle` | Enum | Optional | Default settlement cycle | `"T_PLUS_2"` |
| 33 | `settlementInstructions` | String | Optional | Settlement instructions | `"DTC clearing"` |

### 6.9 Account Relationships & Metadata

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 34 | `clientId` | ID | Required | Owning client identifier | `"CLI-001234"` |
| 35 | `createdAt` | DateTime | Required | Record creation timestamp | `"2024-01-15T09:00:00Z"` |
| 36 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T14:00:00Z"` |

---

## 7. Portfolio Data Fields

### 7.1 Portfolio Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique portfolio identifier | `"PF-001234"` |
| 2 | `portfolioName` | String | Required | Portfolio name | `"Growth Portfolio"` |
| 3 | `portfolioCode` | String | Optional | Portfolio code | `"GRW-001"` |
| 4 | `baseCurrency` | String | Required | Base currency per ISO 4217 | `"USD"` |
| 5 | `strategy` | Enum | Optional | Portfolio strategy | See [Appendix A.7](#a7-portfoliostrategy) |
| 6 | `benchmark` | String | Optional | Benchmark name | `"S&P 500"` |
| 7 | `benchmarkIsin` | String | Optional | Benchmark ISIN | `"US78378X1072"` |

### 7.2 Portfolio Valuation

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 8 | `marketValue` | Decimal | Optional | Total market value | `1855000.00` |
| 9 | `costBasis` | Decimal | Optional | Total cost basis | `1650000.00` |
| 10 | `unrealizedPnL` | Decimal | Optional | Unrealized profit/loss | `205000.00` |
| 11 | `realizedPnL` | Decimal | Optional | Realized profit/loss | `45000.00` |
| 12 | `totalReturn` | Decimal | Optional | Total return (%) | `15.20` |
| 13 | `valuationDate` | DateTime | Optional | Valuation date | `"2026-02-08T16:00:00Z"` |
| 14 | `valuationCurrency` | String | Optional | Valuation currency per ISO 4217 | `"USD"` |

### 7.3 Portfolio Risk Metrics

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 15 | `volatility` | Decimal | Optional | Portfolio volatility (%) | `12.50` |
| 16 | `sharpeRatio` | Decimal | Optional | Sharpe ratio | `1.85` |
| 17 | `beta` | Decimal | Optional | Portfolio beta | `1.10` |
| 18 | `maxDrawdown` | Decimal | Optional | Maximum drawdown (%) | `8.30` |
| 19 | `valueAtRisk` | Decimal | Optional | Value at Risk | `92500.00` |

### 7.4 Portfolio Asset Allocation

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 20 | `equityWeight` | Decimal | Optional | Equity allocation (%) | `60.00` |
| 21 | `fixedIncomeWeight` | Decimal | Optional | Fixed income allocation (%) | `25.00` |
| 22 | `cashWeight` | Decimal | Optional | Cash allocation (%) | `10.00` |
| 23 | `alternativesWeight` | Decimal | Optional | Alternatives allocation (%) | `5.00` |

### 7.5 Portfolio Investment Guidelines

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 24 | `targetReturn` | Decimal | Optional | Target return (%) | `8.00` |
| 25 | `riskBudget` | Decimal | Optional | Risk budget (%) | `15.00` |
| 26 | `maxSinglePositionWeight` | Decimal | Optional | Max single position weight (%) | `10.00` |
| 27 | `maxSectorWeight` | Decimal | Optional | Max sector weight (%) | `25.00` |
| 28 | `maxCountryWeight` | Decimal | Optional | Max country weight (%) | `40.00` |

### 7.6 Portfolio Compliance

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 29 | `investmentPolicyCompliant` | Boolean | Optional | Investment policy compliance flag | `true` |
| 30 | `lastComplianceCheckDate` | DateTime | Optional | Last compliance check date | `"2026-02-01T00:00:00Z"` |
| 31 | `complianceBreaches` | Int | Optional | Number of compliance breaches | `0` |

### 7.7 Portfolio Relationships & Metadata

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 32 | `accountId` | ID | Required | Parent account identifier | `"ACC-001234"` |
| 33 | `inceptionDate` | DateTime | Optional | Portfolio inception date | `"2024-01-15T00:00:00Z"` |
| 34 | `lastRebalanceDate` | DateTime | Optional | Last rebalance date | `"2026-01-15T00:00:00Z"` |
| 35 | `nextRebalanceDate` | DateTime | Optional | Next scheduled rebalance date | `"2026-04-15T00:00:00Z"` |
| 36 | `createdAt` | DateTime | Required | Record creation timestamp | `"2024-01-15T09:00:00Z"` |
| 37 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T14:00:00Z"` |

---

## 8. Position Data Fields

### 8.1 Position Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique position identifier | `"POS-001234"` |
| 2 | `assetId` | ID | Required | Reference to security/asset record | `"SEC-EQ-001"` |
| 3 | `assetClass` | Enum | Required | Asset class | `"SECURITIES"` |
| 4 | `isin` | String | Optional | ISIN of the held asset | `"US0378331005"` |
| 5 | `ticker` | String | Optional | Ticker symbol | `"AAPL"` |
| 6 | `assetName` | String | Required | Asset name | `"Apple Inc. Common Stock"` |

### 8.2 Position Quantity

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 7 | `quantity` | Decimal | Required | Holding quantity | `10000` |
| 8 | `side` | Enum | Required | Position side | See [Appendix A.8](#a8-positionside) |
| 9 | `status` | Enum | Required | Position status | See [Appendix A.9](#a9-positionstatus) |

### 8.3 Position Cost & Valuation

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 10 | `averageCost` | Decimal | Required | Average cost per unit | `165.00` |
| 11 | `costBasis` | Decimal | Required | Total cost basis (quantity × averageCost) | `1650000.00` |
| 12 | `costCurrency` | String | Required | Cost currency per ISO 4217 | `"USD"` |
| 13 | `currentPrice` | Decimal | Optional | Current market price | `185.50` |
| 14 | `marketValue` | Decimal | Optional | Current market value | `1855000.00` |
| 15 | `valuationCurrency` | String | Optional | Valuation currency per ISO 4217 | `"USD"` |
| 16 | `valuationDate` | DateTime | Optional | Valuation date | `"2026-02-08T16:00:00Z"` |

### 8.4 Position P&L

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 17 | `unrealizedPnL` | Decimal | Optional | Unrealized profit/loss | `205000.00` |
| 18 | `unrealizedPnLPercent` | Decimal | Optional | Unrealized P&L (%) | `12.42` |
| 19 | `realizedPnL` | Decimal | Optional | Realized profit/loss | `45000.00` |
| 20 | `totalPnL` | Decimal | Optional | Total P&L | `250000.00` |

### 8.5 Position Income

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 21 | `accruedInterest` | Decimal | Optional | Accrued interest | `0.00` |
| 22 | `accruedDividend` | Decimal | Optional | Accrued dividend | `550.00` |

### 8.6 Position Weight & Settlement

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 23 | `portfolioWeight` | Decimal | Optional | Weight in portfolio (%) | `15.50` |
| 24 | `settledQuantity` | Decimal | Optional | Settled quantity | `10000` |
| 25 | `unsettledQuantity` | Decimal | Optional | Unsettled quantity | `0` |

### 8.7 Position Dates & Relationships

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 26 | `openDate` | DateTime | Required | Position open date | `"2024-06-15T10:30:00Z"` |
| 27 | `lastTradeDate` | DateTime | Optional | Last trade date | `"2026-01-20T14:00:00Z"` |
| 28 | `maturityDate` | DateTime | Optional | Maturity date (bonds) | `null` |
| 29 | `accountId` | ID | Required | Parent account identifier | `"ACC-001234"` |
| 30 | `portfolioId` | ID | Optional | Parent portfolio identifier | `"PF-001234"` |
| 31 | `createdAt` | DateTime | Required | Record creation timestamp | `"2024-06-15T10:30:00Z"` |
| 32 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T16:00:00Z"` |

---

## 9. Transaction Data Fields

### 9.1 Transaction Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique transaction identifier | `"TXN-001234"` |
| 2 | `transactionReference` | String | Required | Transaction reference number | `"REF-2026-001234"` |
| 3 | `instructionReference` | String | Optional | Instruction reference | `"INST-2026-001"` |
| 4 | `tradeId` | String | Optional | Trade identifier | `"TRD-2026-001234"` |

### 9.2 Transaction Details

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 5 | `transactionType` | Enum | Required | Transaction type | See [Appendix A.10](#a10-transactiontype) |
| 6 | `status` | Enum | Required | Transaction status | See [Appendix A.11](#a11-transactionstatus) |

### 9.3 Transaction Instrument

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 7 | `assetId` | ID | Optional | Reference to security/asset record | `"SEC-EQ-001"` |
| 8 | `assetClass` | Enum | Optional | Asset class | `"SECURITIES"` |
| 9 | `isin` | String | Optional | ISIN of the traded asset | `"US0378331005"` |
| 10 | `assetName` | String | Optional | Asset name | `"Apple Inc. Common Stock"` |

### 9.4 Transaction Quantity & Price

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 11 | `quantity` | Decimal | Optional | Quantity traded | `500` |
| 12 | `price` | Decimal | Optional | Execution price | `185.50` |
| 13 | `priceCurrency` | String | Optional | Price currency per ISO 4217 | `"USD"` |
| 14 | `grossAmount` | Decimal | Required | Gross amount | `92750.00` |
| 15 | `netAmount` | Decimal | Required | Net amount (after fees) | `92700.00` |
| 16 | `currency` | String | Required | Transaction currency per ISO 4217 | `"USD"` |
| 17 | `exchangeRate` | Decimal | Optional | FX exchange rate (if cross-currency) | `1.00` |

### 9.5 Transaction Fees & Charges

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 18 | `commission` | Decimal | Optional | Commission | `25.00` |
| 19 | `brokerageFee` | Decimal | Optional | Brokerage fee | `15.00` |
| 20 | `stampDuty` | Decimal | Optional | Stamp duty | `0.00` |
| 21 | `tax` | Decimal | Optional | Tax | `10.00` |
| 22 | `otherFees` | Decimal | Optional | Other fees | `0.00` |
| 23 | `totalFees` | Decimal | Optional | Total fees | `50.00` |
| 24 | `feeCurrency` | String | Optional | Fee currency per ISO 4217 | `"USD"` |

### 9.6 Transaction Dates

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 25 | `tradeDate` | DateTime | Required | Trade date | `"2026-02-08T14:30:00Z"` |
| 26 | `settlementDate` | DateTime | Optional | Settlement date | `"2026-02-10T00:00:00Z"` |
| 27 | `valueDate` | DateTime | Optional | Value date | `"2026-02-10T00:00:00Z"` |
| 28 | `bookingDate` | DateTime | Optional | Booking date | `"2026-02-08T16:00:00Z"` |

### 9.7 Transaction Settlement

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 29 | `settlementStatus` | String | Optional | Settlement status | `"SETTLED"` |
| 30 | `settlementType` | Enum | Optional | Settlement type | See [Appendix A.16](#a16-settlementtype) |
| 31 | `settlementCurrency` | String | Optional | Settlement currency per ISO 4217 | `"USD"` |
| 32 | `settlementAmount` | Decimal | Optional | Settlement amount | `92700.00` |

### 9.8 Transaction Counterparty

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 33 | `counterparty` | String | Optional | Counterparty name | `"Goldman Sachs"` |
| 34 | `counterpartyLei` | String | Optional | Counterparty LEI per ISO 17442 | `"784F5XWPLTWKTBV3E584"` |
| 35 | `broker` | String | Optional | Broker name | `"Morgan Stanley"` |
| 36 | `brokerLei` | String | Optional | Broker LEI per ISO 17442 | `"IGJSJL3JD5P30I6NJZ34"` |
| 37 | `executionVenue` | String | Optional | Execution venue | `"NASDAQ"` |
| 38 | `mic` | String | Optional | Market Identifier Code per ISO 10383 | `"XNAS"` |

### 9.9 Transaction Relationships & Metadata

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 39 | `accountId` | ID | Required | Parent account identifier | `"ACC-001234"` |
| 40 | `portfolioId` | ID | Optional | Portfolio identifier | `"PF-001234"` |
| 41 | `positionId` | ID | Optional | Related position identifier | `"POS-001234"` |
| 42 | `orderId` | ID | Optional | Originating order identifier | `"ORD-001234"` |
| 43 | `createdAt` | DateTime | Required | Record creation timestamp | `"2026-02-08T14:30:00Z"` |
| 44 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-10T16:00:00Z"` |

---

## 10. FX Deposit Data Fields

### 10.1 Deposit Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique deposit identifier | `"FXD-001234"` |
| 2 | `depositReference` | String | Required | Deposit reference number | `"DEP-2026-001234"` |
| 3 | `depositType` | Enum | Required | Deposit type | See [Appendix A.17](#a17-deposittype) |
| 4 | `status` | Enum | Required | Deposit status | See [Appendix A.18](#a18-depositstatus) |

### 10.2 Deposit Currency & Amount

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 5 | `currency` | String | Required | Deposit currency per ISO 4217 | `"USD"` |
| 6 | `principalAmount` | Decimal | Required | Principal amount | `1000000.00` |
| 7 | `currentBalance` | Decimal | Optional | Current balance | `1012500.00` |

### 10.3 Deposit Interest

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 8 | `interestType` | Enum | Required | Interest type | See [Appendix A.21](#a21-interesttype) |
| 9 | `interestRate` | Decimal | Required | Interest rate (%) | `5.00` |
| 10 | `floatingRateIndex` | Enum | Optional | Floating rate index (if floating) | See [Appendix A.22](#a22-floatingrateindex) |
| 11 | `floatingRateSpread` | Decimal | Optional | Spread over floating rate (bps) | `50.00` |
| 12 | `accruedInterest` | Decimal | Optional | Accrued interest | `12500.00` |
| 13 | `interestAmount` | Decimal | Optional | Total interest earned | `12500.00` |
| 14 | `interestCurrency` | String | Optional | Interest currency per ISO 4217 | `"USD"` |
| 15 | `dayCountBasis` | Enum | Optional | Day count convention | `"ACT_360"` |
| 16 | `interestPaymentFrequency` | Enum | Optional | Interest payment frequency | `"MONTHLY"` |
| 17 | `compoundingFrequency` | Enum | Optional | Compounding frequency | `"MONTHLY"` |

### 10.4 Deposit Term & Maturity

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 18 | `termType` | Enum | Required | Term type | See [Appendix A.19](#a19-deposittermtype) |
| 19 | `tenorDays` | Int | Optional | Tenor in days | `90` |
| 20 | `noticePeriodDays` | Int | Optional | Notice period in days | `0` |
| 21 | `startDate` | DateTime | Required | Deposit start date | `"2026-01-15T00:00:00Z"` |
| 22 | `maturityDate` | DateTime | Optional | Maturity date | `"2026-04-15T00:00:00Z"` |
| 23 | `valueDate` | DateTime | Optional | Value date | `"2026-01-15T00:00:00Z"` |

### 10.5 Deposit Rollover

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 24 | `rolloverType` | Enum | Optional | Rollover type | See [Appendix A.20](#a20-rollovertype) |
| 25 | `rolloverCount` | Int | Optional | Number of rollovers | `0` |
| 26 | `nextRolloverDate` | DateTime | Optional | Next rollover date | `null` |

### 10.6 Deposit Early Withdrawal

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 27 | `earlyWithdrawalAllowed` | Boolean | Optional | Early withdrawal allowed flag | `true` |
| 28 | `earlyWithdrawalPenaltyRate` | Decimal | Optional | Early withdrawal penalty rate (%) | `1.00` |
| 29 | `breakCost` | Decimal | Optional | Break cost | `0.00` |
| 30 | `minimumBalance` | Decimal | Optional | Minimum balance | `100000.00` |

### 10.7 Deposit Servicing Institution

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 31 | `depositBank` | String | Optional | Deposit bank name | `"Citibank"` |
| 32 | `depositBankLei` | String | Optional | Deposit bank LEI per ISO 17442 | `"6SHGI4ZSSLCXXQSBB395"` |
| 33 | `depositBankBic` | String | Optional | Deposit bank BIC per ISO 9362 | `"CITIUS33XXX"` |
| 34 | `branchCode` | String | Optional | Branch code | `"NYC-001"` |

### 10.8 Deposit Settlement

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 35 | `settlementAccount` | String | Optional | Settlement account | `"1234567890"` |
| 36 | `settlementCurrency` | String | Optional | Settlement currency per ISO 4217 | `"USD"` |
| 37 | `settlementInstructions` | String | Optional | Settlement instructions | `"Wire transfer"` |

### 10.9 Deposit Tax

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 38 | `withholdingTaxRate` | Decimal | Optional | Withholding tax rate (%) | `30.00` |
| 39 | `taxStatus` | Enum | Optional | Tax status | See [Appendix A.15](#a15-taxstatus) |

### 10.10 Deposit Source Fund & Relationships

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 40 | `sourceAccountId` | ID | Optional | Linked source account identifier | `"ACC-001234"` |
| 41 | `fundingTransactionRef` | String | Optional | Funding transaction reference | `"TXN-FND-001"` |
| 42 | `accountId` | ID | Required | Parent account identifier | `"ACC-001234"` |
| 43 | `clientId` | ID | Required | Client identifier | `"CLI-001234"` |
| 44 | `createdAt` | DateTime | Required | Record creation timestamp | `"2026-01-15T09:00:00Z"` |
| 45 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T14:00:00Z"` |

---

## 11. Order Data Fields

### 11.1 Order Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | ID | Required | Unique order identifier | `"ORD-001234"` |
| 2 | `orderReference` | String | Required | Order reference number | `"REF-ORD-2026-001"` |
| 3 | `clientOrderId` | String | Optional | Client-assigned order ID | `"CO-2026-001"` |

### 11.2 Order Details

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 4 | `side` | Enum | Required | Order side | See [Appendix A.12](#a12-orderside) |
| 5 | `orderType` | Enum | Required | Order type | See [Appendix A.13](#a13-ordertype) |
| 6 | `status` | Enum | Required | Order status | See [Appendix A.14](#a14-orderstatus) |
| 7 | `timeInForce` | Enum | Required | Time in force | See [Appendix A.23](#a23-timeinforce) |

### 11.3 Order Instrument

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 8 | `assetId` | ID | Required | Reference to security/asset record | `"SEC-EQ-001"` |
| 9 | `assetClass` | Enum | Required | Asset class | `"SECURITIES"` |
| 10 | `isin` | String | Optional | ISIN of the ordered asset | `"US0378331005"` |
| 11 | `assetName` | String | Required | Asset name | `"Apple Inc. Common Stock"` |

### 11.4 Order Quantity & Price

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 12 | `quantity` | Decimal | Required | Order quantity | `500` |
| 13 | `filledQuantity` | Decimal | Optional | Filled quantity | `500` |
| 14 | `remainingQuantity` | Decimal | Optional | Remaining quantity | `0` |
| 15 | `limitPrice` | Decimal | Optional | Limit price | `186.00` |
| 16 | `stopPrice` | Decimal | Optional | Stop price | `null` |
| 17 | `averageFilledPrice` | Decimal | Optional | Average filled price | `185.50` |
| 18 | `currency` | String | Required | Order currency per ISO 4217 | `"USD"` |

### 11.5 Order Validity & Execution

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 19 | `expiryDate` | DateTime | Optional | Order expiry date | `"2026-02-08T16:00:00Z"` |
| 20 | `executionVenue` | String | Optional | Execution venue | `"NASDAQ"` |
| 21 | `mic` | String | Optional | Market Identifier Code per ISO 10383 | `"XNAS"` |

### 11.6 Order Dates

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 22 | `orderDate` | DateTime | Required | Order creation date | `"2026-02-08T14:00:00Z"` |
| 23 | `submittedDate` | DateTime | Optional | Order submission date | `"2026-02-08T14:00:05Z"` |
| 24 | `lastFilledDate` | DateTime | Optional | Last fill date | `"2026-02-08T14:30:00Z"` |
| 25 | `cancelledDate` | DateTime | Optional | Cancellation date | `null` |

### 11.7 Order Relationships & Metadata

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 26 | `accountId` | ID | Required | Parent account identifier | `"ACC-001234"` |
| 27 | `portfolioId` | ID | Optional | Portfolio identifier | `"PF-001234"` |
| 28 | `requestedBy` | ID | Optional | Requester (RM or Client ID) | `"RM-001"` |
| 29 | `approvedBy` | ID | Optional | Approver identifier | `"RM-000"` |
| 30 | `createdAt` | DateTime | Required | Record creation timestamp | `"2026-02-08T14:00:00Z"` |
| 31 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-08T14:30:00Z"` |

---

## Appendix A: Enum Reference Tables

### A.1 ClientType

| Value | Description |
|---|---|
| `INDIVIDUAL` | Individual/personal client |
| `CORPORATE` | Corporate entity |
| `INSTITUTIONAL` | Institutional investor |
| `TRUST` | Trust account |
| `JOINT` | Joint account holders |
| `MINOR` | Minor (under legal age) |
| `NON_PROFIT` | Non-profit organization |

### A.2 ClientStatus

| Value | Description |
|---|---|
| `ACTIVE` | Active client |
| `INACTIVE` | Inactive client |
| `SUSPENDED` | Suspended |
| `CLOSED` | Closed |
| `PENDING_APPROVAL` | Pending approval |
| `DORMANT` | Dormant account |

### A.3 KYCStatus

| Value | Description |
|---|---|
| `VERIFIED` | KYC verified |
| `PENDING` | Verification pending |
| `EXPIRED` | Verification expired |
| `REJECTED` | Verification rejected |
| `UNDER_REVIEW` | Under review |

### A.4 ClientRiskProfile

| Value | Description |
|---|---|
| `CONSERVATIVE` | Conservative risk tolerance |
| `MODERATELY_CONSERVATIVE` | Moderately conservative |
| `MODERATE` | Moderate risk tolerance |
| `MODERATELY_AGGRESSIVE` | Moderately aggressive |
| `AGGRESSIVE` | Aggressive risk tolerance |

### A.5 AccountType

| Value | Description |
|---|---|
| `CASH` | Cash account |
| `MARGIN` | Margin account |
| `CUSTODY` | Custody account |
| `DISCRETIONARY` | Discretionary managed account |
| `ADVISORY` | Advisory account |
| `EXECUTION_ONLY` | Execution-only account |
| `TRUST` | Trust account |
| `RETIREMENT` | Retirement account |
| `JOINT` | Joint account |

### A.6 AccountStatus

| Value | Description |
|---|---|
| `ACTIVE` | Active |
| `INACTIVE` | Inactive |
| `SUSPENDED` | Suspended |
| `CLOSED` | Closed |
| `FROZEN` | Frozen |
| `PENDING_APPROVAL` | Pending approval |

### A.7 PortfolioStrategy

| Value | Description |
|---|---|
| `GROWTH` | Growth-oriented strategy |
| `INCOME` | Income-focused strategy |
| `BALANCED` | Balanced growth and income |
| `CAPITAL_PRESERVATION` | Capital preservation |
| `AGGRESSIVE_GROWTH` | Aggressive growth |
| `INDEX_TRACKING` | Index tracking/passive |
| `ABSOLUTE_RETURN` | Absolute return strategy |

### A.8 PositionSide

| Value | Description |
|---|---|
| `LONG` | Long position |
| `SHORT` | Short position |

### A.9 PositionStatus

| Value | Description |
|---|---|
| `OPEN` | Open position |
| `CLOSED` | Closed position |
| `PARTIALLY_CLOSED` | Partially closed |

### A.10 TransactionType

| Value | Description |
|---|---|
| `BUY` | Purchase |
| `SELL` | Sale |
| `TRANSFER_IN` | Transfer in |
| `TRANSFER_OUT` | Transfer out |
| `DIVIDEND` | Dividend payment |
| `INTEREST` | Interest payment |
| `COUPON` | Coupon payment |
| `REDEMPTION` | Redemption |
| `SUBSCRIPTION` | Subscription |
| `FEE` | Fee charge |
| `TAX` | Tax charge |
| `CORPORATE_ACTION` | Corporate action |
| `FX_CONVERSION` | FX conversion |
| `MARGIN_CALL` | Margin call |

### A.11 TransactionStatus

| Value | Description |
|---|---|
| `PENDING` | Pending |
| `EXECUTED` | Executed |
| `SETTLED` | Settled |
| `FAILED` | Failed |
| `CANCELLED` | Cancelled |
| `PARTIALLY_FILLED` | Partially filled |
| `AWAITING_SETTLEMENT` | Awaiting settlement |

### A.12 OrderSide

| Value | Description |
|---|---|
| `BUY` | Buy order |
| `SELL` | Sell order |

### A.13 OrderType

| Value | Description |
|---|---|
| `MARKET` | Market order |
| `LIMIT` | Limit order |
| `STOP` | Stop order |
| `STOP_LIMIT` | Stop-limit order |
| `TRAILING_STOP` | Trailing stop order |

### A.14 OrderStatus

| Value | Description |
|---|---|
| `PENDING` | Pending |
| `SUBMITTED` | Submitted |
| `PARTIALLY_FILLED` | Partially filled |
| `FILLED` | Fully filled |
| `CANCELLED` | Cancelled |
| `REJECTED` | Rejected |
| `EXPIRED` | Expired |

### A.15 TaxStatus

| Value | Description |
|---|---|
| `TAXABLE` | Taxable |
| `TAX_EXEMPT` | Tax exempt |
| `TAX_DEFERRED` | Tax deferred |
| `WITHHOLDING` | Subject to withholding |
| `NOT_APPLICABLE` | Not applicable |

### A.16 SettlementType

| Value | Description |
|---|---|
| `CASH` | Cash settlement |
| `PHYSICAL` | Physical delivery |
| `NET_CASH` | Net cash settlement |
| `DVP` | Delivery versus payment |
| `FOP` | Free of payment |
| `RVP` | Receive versus payment |

### A.17 DepositType

| Value | Description |
|---|---|
| `SAVINGS` | Savings deposit |
| `FIXED_TERM` | Fixed-term deposit |
| `CALL_DEPOSIT` | Call deposit |
| `NOTICE_DEPOSIT` | Notice deposit |
| `OVERNIGHT` | Overnight deposit |
| `TIME_DEPOSIT` | Time deposit |
| `DEMAND_DEPOSIT` | Demand deposit |
| `STRUCTURED_DEPOSIT` | Structured deposit |

### A.18 DepositStatus

| Value | Description |
|---|---|
| `ACTIVE` | Active deposit |
| `MATURED` | Matured |
| `BROKEN` | Early termination |
| `PENDING_ACTIVATION` | Pending activation |
| `ROLLED_OVER` | Rolled over |
| `CLOSED` | Closed |

### A.19 DepositTermType

| Value | Description |
|---|---|
| `OPEN_ENDED` | Open-ended |
| `FIXED_TERM` | Fixed term |
| `NOTICE_PERIOD` | Notice period |
| `OVERNIGHT` | Overnight |
| `WEEKLY` | Weekly |
| `MONTHLY` | Monthly |
| `QUARTERLY` | Quarterly |
| `SEMI_ANNUAL` | Semi-annual |
| `ANNUAL` | Annual |

### A.20 RolloverType

| Value | Description |
|---|---|
| `NONE` | No rollover |
| `PRINCIPAL_ONLY` | Principal only |
| `PRINCIPAL_AND_INTEREST` | Principal and interest |
| `AUTOMATIC` | Automatic rollover |
| `MANUAL` | Manual rollover |

### A.21 InterestType

| Value | Description |
|---|---|
| `FIXED` | Fixed interest rate |
| `FLOATING` | Floating interest rate |
| `ZERO_COUPON` | Zero coupon |
| `STEP_UP` | Step-up rate |
| `STEP_DOWN` | Step-down rate |
| `VARIABLE` | Variable rate |

### A.22 FloatingRateIndex

| Value | Description |
|---|---|
| `LIBOR` | London Interbank Offered Rate |
| `SOFR` | Secured Overnight Financing Rate |
| `EURIBOR` | Euro Interbank Offered Rate |
| `SONIA` | Sterling Overnight Index Average |
| `TONAR` | Tokyo Overnight Average Rate |
| `ESTR` | Euro Short-Term Rate |
| `SARON` | Swiss Average Rate Overnight |
| `PRIME_RATE` | Prime rate |

### A.23 TimeInForce

| Value | Description |
|---|---|
| `DAY` | Day order |
| `GTC` | Good till cancelled |
| `GTD` | Good till date |
| `IOC` | Immediate or cancel |
| `FOK` | Fill or kill |
| `OPG` | At the opening |
| `CLO` | At the close |

---

*End of Document*
