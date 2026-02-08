# Vahalla Wealth Data Specification

| | |
|---|---|
| **Document Title** | Investment Module - Equity & Bond Specification |
| **System** | Vahalla Wealth Management System |
| **Document Version** | 1.0 |
| **Date** | 2025-09-10 |
| **Classification** | Confidential |
| **Status** | Draft |
| **Prepared By** | Vahalla System Team |

### Revision History

| Version | Date | Author | Description |
|---|---|---|---|
| 1.0 | 2025-09-10 | Vahalla System Team | Initial draft — Equity & Bond specification |

---

## 1. Purpose

This document specifies the data fields, formats, and standards required from banking partners to integrate securities data into our Wealth Management System. The data model is aligned with the **ISO 20022** financial messaging standard to ensure interoperability, consistency, and regulatory compliance across all partners. The system consolidates investment data from multiple banks and presents it to Relationship Managers (RMs) for portfolio management and client advisory.

**This version covers Equity and Bond (Fixed Income) asset classes only.** Specifications for other asset classes (Derivatives, Investment Funds, FX, Structured Products, Commodities, Money Market) will be provided separately.

---

## 2. Entity Relationship Overview

The following diagram illustrates the relationships between the core entities in the Vahalla Wealth Management System.

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
    Asset -->|is a| Equity
    Asset -->|is a| Bond
```

---

## 3. Data Standards & Conventions

### 3.1 Applicable ISO Standards

| Standard | Description | Usage |
|---|---|---|
| **ISO 6166** | ISIN — International Securities Identification Number | Primary security identifier (12 chars) |
| **ISO 6167** | CUSIP — Committee on Uniform Securities Identification Procedures | US/Canada security identifier (9 chars) |
| **SEDOL** | Stock Exchange Daily Official List | UK/Ireland security identifier (7 chars) |
| **ISO 17442** | LEI — Legal Entity Identifier | Issuer identification (20 chars) |
| **ISO 18774** | FISN — Financial Instrument Short Name | Standardized short name (max 35 chars) |
| **ISO 10962** | CFI — Classification of Financial Instruments | Instrument classification (6 chars) |
| **ISO 10383** | MIC — Market Identifier Code | Exchange/venue identification (4 chars) |
| **ISO 4217** | Currency Codes | All currency fields (3 chars, e.g. USD, EUR, THB) |
| **ISO 3166** | Country Codes | Country of issuance (2-char alpha) |
| **ISO 8601** | Date/Time Format | All date and timestamp fields |
| **ISO 20022** | Financial Messaging Standard | Overall data model alignment |

### 3.2 Data Type Definitions

| Data Type | Format | Example |
|---|---|---|
| `String` | UTF-8 text | `"Apple Inc."` |
| `Decimal` | Numeric with decimal precision (up to 18 digits) | `150.2500` |
| `Int` | Integer | `100` |
| `Boolean` | `true` / `false` | `true` |
| `Date` | ISO 8601 date: `YYYY-MM-DD` | `2026-01-15` |
| `DateTime` | ISO 8601 with timezone: `YYYY-MM-DDTHH:MM:SSZ` | `2026-01-15T09:30:00Z` |
| `Enum` | Predefined value from allowed list | `COMMON` |

### 3.3 Field Requirement Levels

| Level | Label | Meaning |
|---|---|---|
| **Required** | Required | Must be provided. Record will be rejected without this field. |
| **Conditionally Required** | Conditional | Required when a specific condition is met (noted in Description). |
| **Optional** | Optional | Provide if available. Enhances data quality. |

### 3.4 Delivery Format

- **Preferred:** JSON or XML (ISO 20022 compliant messages)
- **Acceptable:** CSV with header row matching field names, or plain text (fixed-width/delimited)
- **Encoding:** UTF-8
- **Frequency:** Daily end-of-day batch, or real-time via API

---

## 4. Common Security Identification Fields

These fields apply to **all securities** (Equity and Bond). Every record must include these fields.

### 4.1 Primary Identification

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 1 | `id` | String | Required | Unique identifier assigned by the bank for this security record | `"SEC-001234"` | reda.041 — `SctyId/OthrId/Id` |
| 2 | `isin` | String | Required | ISIN code per ISO 6166 (12 chars) | `"US0378331005"` | reda.041 — `SctyId/ISIN` |
| 3 | `cusip` | String | Optional | CUSIP identifier (9 chars, US/Canada securities) | `"037833100"` | reda.041 — `SctyId/OthrId/Id` (CUSIP) |
| 4 | `sedol` | String | Optional | SEDOL identifier (7 chars, UK/Ireland securities) | `"2046251"` | reda.041 — `SctyId/OthrId/Id` (SEDOL) |
| 5 | `ticker` | String | Optional | Exchange ticker symbol | `"AAPL"` | reda.041 — `SctyId/TckrSymb` |
| 6 | `name` | String | Required | Full legal name of the security | `"Apple Inc. Common Stock"` | reda.041 — `FinInstrmAttrbts/Nm` |
| 7 | `assetClass` | Enum | Required | Must be `SECURITIES` for all records in this specification | `"SECURITIES"` | reda.041 — `FinInstrmAttrbts/ClssfctnTp` |

### 4.2 Issuer & Classification

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 8 | `issuer` | String | Optional | Name of the issuing entity | `"Apple Inc."` | reda.041 — `Issr/Nm` |
| 9 | `lei` | String | Optional | Legal Entity Identifier of issuer per ISO 17442 (20 chars) | `"HWUPKR0MPOU8FGXBT394"` | reda.041 — `Issr/LEI` |
| 10 | `fisn` | String | Optional | Financial Instrument Short Name per ISO 18774 (max 35 chars) | `"APPLE INC/SH"` | reda.041 — `FinInstrmAttrbts/FISN` |
| 11 | `cfi` | String | Optional | Classification of Financial Instruments per ISO 10962 (6 chars) | `"ESVUFR"` | reda.041 — `FinInstrmAttrbts/CFICd` |
| 12 | `currency` | String | Required | Trading/denomination currency per ISO 4217 (3 chars) | `"USD"` | reda.041 — `FinInstrmAttrbts/DnmtnCcy` |
| 13 | `country` | String | Optional | Country of issuance per ISO 3166-1 alpha-2 | `"US"` | reda.041 — `FinInstrmAttrbts/CtryOfIsse` |
| 14 | `description` | String | Optional | Free-text description of the security | `"Common shares of Apple Inc."` | reda.041 — `FinInstrmAttrbts/Desc` |
| 15 | `documentUrl` | String | Optional | URL to fund fact sheet, prospectus, or key information document (KID) | `"https://bank.com/docs/AAPL-factsheet.pdf"` | reda.041 — `AddtlInf/InfURL` |

### 4.3 Security Common Fields

| # | Field Name | Data Type | Required | Description | Allowed Values / Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 16 | `securityType` | Enum | Required | Top-level security classification | See [Appendix A.1](#a1-securitytype) | reda.041 — `FinInstrmAttrbts/ClssfctnTp` |
| 17 | `exchange` | String | Optional | Primary exchange name | `"NASDAQ"` | reda.041 — `TradgDtls/PlcOfListg/Nm` |
| 18 | `mic` | String | Optional | Market Identifier Code per ISO 10383 (4 chars) | `"XNAS"` | reda.041 — `TradgDtls/PlcOfListg/MktIdCd` |
| 19 | `tradingStatus` | Enum | Optional | Current trading status | See [Appendix A.2](#a2-tradingstatus) | reda.041 — `TradgDtls/TradgSts` |
| 20 | `formOfSecurity` | Enum | Optional | Form of security holding | See [Appendix A.3](#a3-formofsecurity) | reda.041 — `FinInstrmAttrbts/SctiesForm` |
| 21 | `settlementCycle` | Enum | Optional | Standard settlement cycle | See [Appendix A.4](#a4-settlementcycle) | reda.041 — `TradgDtls/SttlmCycl` |
| 22 | `minimumDenomination` | Decimal | Optional | Minimum tradeable denomination | `1000.00` | reda.041 — `FinInstrmAttrbts/MinDnmtn` |
| 23 | `listingDate` | Date | Optional | Date the security was listed on the exchange | `"2024-03-15"` | reda.041 — `TradgDtls/ListgDt` |
| 24 | `maturityDate` | Date | Optional | Maturity date (applicable to bonds/notes) | `"2034-03-15"` | reda.041 — `FinInstrmAttrbts/MtrtyDt` |

### 4.4 Timestamps

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 25 | `createdAt` | DateTime | Required | Record creation timestamp | `"2026-01-15T09:30:00Z"` | reda.041 — `CreDtTm` |
| 26 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-01T14:00:00Z"` | reda.041 — `UpdDtTm` |

---

## 5. Equity Data Fields

For records where `securityType` = `EQUITY`, `PREFERRED_STOCK`, `CONVERTIBLE_BOND`, or `WARRANT`.

### 5.1 Equity-Specific Fields

| # | Field Name | Data Type | Required | Description | Allowed Values / Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 27 | `equityType` | Enum | Required | Specific equity sub-type | See [Appendix A.5](#a5-equitytype) | reda.041 — `Eqty/EqtyTp` |
| 28 | `sector` | String | Optional | GICS or ICB sector classification | `"Information Technology"` | reda.041 — `Eqty/Sctr` |
| 29 | `industry` | String | Optional | Industry sub-classification | `"Consumer Electronics"` | reda.041 — `Eqty/Indstry` |
| 30 | `marketCap` | Decimal | Optional | Market capitalization in trading currency | `2850000000000.00` | reda.041 — `Eqty/MktCptlstn` |
| 31 | `sharesOutstanding` | Decimal | Optional | Total shares outstanding | `15500000000` | reda.041 — `Eqty/ShrsOutstndg` |
| 32 | `dividendYield` | Decimal | Optional | Annual dividend yield (percentage, e.g. 1.5 = 1.5%) | `0.55` | reda.041 — `Eqty/DvddYld` |
| 33 | `beta` | Decimal | Optional | Beta coefficient vs. benchmark | `1.28` | — (supplementary) |
| 34 | `eps` | Decimal | Optional | Earnings per share (trailing 12 months) | `6.42` | reda.041 — `Eqty/ErngsPerShr` |
| 35 | `peRatio` | Decimal | Optional | Price-to-earnings ratio | `29.50` | reda.041 — `Eqty/PricErngsRatio` |
| 36 | `votingRights` | Boolean | Optional | Whether shares carry voting rights | `true` | reda.041 — `Eqty/VtngRghts` |
| 37 | `dividendFrequency` | Enum | Optional | Dividend payment frequency | See [Appendix A.6](#a6-paymentfrequency) | reda.041 — `Eqty/DvddPmtFrqcy` |
| 38 | `parValue` | Decimal | Optional | Par/nominal value per share | `0.00001` | reda.041 — `FinInstrmAttrbts/ParVal` |
| 39 | `delistingDate` | Date | Optional | Date the security was/will be delisted | `"2026-12-31"` | reda.041 — `TradgDtls/DlstgDt` |
| 40 | `lotSize` | Int | Optional | Standard trading lot size | `100` | reda.041 — `TradgDtls/LotSz` |
| 41 | `boardLot` | Int | Optional | Board lot size (if different from lot size) | `100` | reda.041 — `TradgDtls/BrdLotSz` |
| 42 | `priceMultiplier` | Decimal | Optional | Contract/price multiplier | `1.00` | reda.041 — `TradgDtls/PricMltplr` |
| 43 | `restrictionType` | Enum | Optional | Trading restriction classification | See [Appendix A.7](#a7-restrictiontype) | reda.041 — `TradgDtls/RstrctnTp` |
| 44 | `paymentStatus` | Enum | Optional | Share payment status | See [Appendix A.8](#a8-paymentstatus) | reda.041 — `Eqty/PmtSts` |

### 5.2 Equity Additional Data — Analyst Ratings

Provide if available. Used for RM advisory dashboards.

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 45 | `consensusRating` | String | Optional | Consensus analyst recommendation | `"BUY"`, `"HOLD"`, `"SELL"` | — (supplementary) |
| 46 | `numberOfAnalysts` | Int | Optional | Number of analysts covering | `42` | — (supplementary) |
| 47 | `buyRatings` | Int | Optional | Number of buy recommendations | `30` | — (supplementary) |
| 48 | `holdRatings` | Int | Optional | Number of hold recommendations | `10` | — (supplementary) |
| 49 | `sellRatings` | Int | Optional | Number of sell recommendations | `2` | — (supplementary) |
| 50 | `targetPrice` | Decimal | Optional | Consensus target price | `210.00` | — (supplementary) |
| 51 | `priceTarget12M` | Decimal | Optional | 12-month forward target price | `225.00` | — (supplementary) |

### 5.3 Equity Additional Data — Financial Metrics

Provide if available. Sourced from latest financial statements.

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 52 | `revenue` | Decimal | Optional | Total revenue (trailing 12 months) | `394328000000.00` | — (supplementary) |
| 53 | `revenueGrowth` | Decimal | Optional | Year-over-year revenue growth (%) | `8.10` | — (supplementary) |
| 54 | `netIncome` | Decimal | Optional | Net income (trailing 12 months) | `96995000000.00` | — (supplementary) |
| 55 | `netIncomeGrowth` | Decimal | Optional | Year-over-year net income growth (%) | `10.20` | — (supplementary) |
| 56 | `ebitda` | Decimal | Optional | EBITDA | `130541000000.00` | — (supplementary) |
| 57 | `ebitdaMargin` | Decimal | Optional | EBITDA margin (%) | `33.10` | — (supplementary) |
| 58 | `operatingMargin` | Decimal | Optional | Operating margin (%) | `30.74` | — (supplementary) |
| 59 | `profitMargin` | Decimal | Optional | Net profit margin (%) | `24.60` | — (supplementary) |
| 60 | `roe` | Decimal | Optional | Return on equity (%) | `160.09` | — (supplementary) |
| 61 | `roa` | Decimal | Optional | Return on assets (%) | `28.30` | — (supplementary) |
| 62 | `debtToEquity` | Decimal | Optional | Debt-to-equity ratio | `1.87` | — (supplementary) |
| 63 | `currentRatio` | Decimal | Optional | Current ratio | `0.99` | — (supplementary) |
| 64 | `quickRatio` | Decimal | Optional | Quick ratio | `0.94` | — (supplementary) |
| 65 | `freeCashFlow` | Decimal | Optional | Free cash flow | `111443000000.00` | — (supplementary) |

### 5.4 Equity Additional Data — Valuation Metrics

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 66 | `priceToBook` | Decimal | Optional | Price-to-book ratio | `47.20` | — (supplementary) |
| 67 | `priceToSales` | Decimal | Optional | Price-to-sales ratio | `7.50` | — (supplementary) |
| 68 | `evToEbitda` | Decimal | Optional | Enterprise value to EBITDA | `22.30` | — (supplementary) |
| 69 | `evToSales` | Decimal | Optional | Enterprise value to sales | `7.40` | — (supplementary) |
| 70 | `pegRatio` | Decimal | Optional | Price/earnings-to-growth ratio | `2.80` | — (supplementary) |
| 71 | `dividendPayoutRatio` | Decimal | Optional | Dividend payout ratio (%) | `15.40` | — (supplementary) |
| 72 | `earningsYield` | Decimal | Optional | Earnings yield (%) | `3.39` | — (supplementary) |

### 5.5 Equity Additional Data — Technical Indicators

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 73 | `rsi` | Decimal | Optional | Relative Strength Index (0–100) | `58.30` | — (supplementary) |
| 74 | `macd` | Decimal | Optional | MACD value | `2.15` | — (supplementary) |
| 75 | `sma50` | Decimal | Optional | 50-day Simple Moving Average | `182.50` | — (supplementary) |
| 76 | `sma200` | Decimal | Optional | 200-day Simple Moving Average | `175.20` | — (supplementary) |
| 77 | `ema50` | Decimal | Optional | 50-day Exponential Moving Average | `183.10` | — (supplementary) |
| 78 | `ema200` | Decimal | Optional | 200-day Exponential Moving Average | `176.00` | — (supplementary) |
| 79 | `bollingerUpper` | Decimal | Optional | Bollinger Band upper | `195.00` | — (supplementary) |
| 80 | `bollingerLower` | Decimal | Optional | Bollinger Band lower | `170.00` | — (supplementary) |
| 81 | `atr` | Decimal | Optional | Average True Range | `3.45` | — (supplementary) |
| 82 | `volume` | Decimal | Optional | Latest trading volume | `54230000` | reda.041 — `TradgDtls/Vol` |
| 83 | `averageVolume` | Decimal | Optional | Average daily trading volume (30-day) | `62100000` | reda.041 — `TradgDtls/AvrgVol` |

### 5.6 Equity Additional Data — Ownership

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 84 | `institutionalOwnership` | Decimal | Optional | Institutional ownership (%) | `60.50` | — (supplementary) |
| 85 | `insiderOwnership` | Decimal | Optional | Insider ownership (%) | `0.07` | — (supplementary) |
| 86 | `publicFloat` | Decimal | Optional | Public float (%) | `99.93` | — (supplementary) |
| 87 | `sharesShort` | Decimal | Optional | Number of shares sold short | `120000000` | — (supplementary) |
| 88 | `shortRatio` | Decimal | Optional | Short ratio (days to cover) | `1.90` | — (supplementary) |
| 89 | `shortPercentOfFloat` | Decimal | Optional | Short interest as % of float | `0.80` | — (supplementary) |

---

## 6. Bond (Fixed Income) Data Fields

For records where `securityType` = `BOND`, `CONVERTIBLE_BOND`, or `DEBENTURE`.

### 6.1 Bond-Specific Fields

| # | Field Name | Data Type | Required | Description | Allowed Values / Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 90 | `bondType` | Enum | Required | Bond sub-type classification | See [Appendix A.9](#a9-bondtype) | reda.041 — `Debt/DebtTp` |
| 91 | `faceValue` | Decimal | Required | Par/face value per unit | `1000.00` | reda.041 — `Debt/FaceAmt` |
| 92 | `couponRate` | Decimal | Required | Annual coupon rate (%, e.g. 5.25 = 5.25%) | `5.25` | reda.041 — `Debt/IntrstRate` |
| 93 | `issueDate` | Date | Required | Original issuance date | `"2024-03-15"` | reda.041 — `Debt/IsseDt` |
| 94 | `issuePrice` | Decimal | Optional | Price at issuance (% of face value) | `99.50` | reda.041 — `Debt/IssePric` |
| 95 | `redemptionPrice` | Decimal | Optional | Redemption price (% of face value) | `100.00` | reda.041 — `Debt/RedPric` |
| 96 | `paymentFrequency` | Enum | Optional | Coupon payment frequency | See [Appendix A.6](#a6-paymentfrequency) | reda.041 — `Debt/IntrstPmtFrqcy` |
| 97 | `dayCountBasis` | Enum | Optional | Day count convention for interest calculation | See [Appendix A.10](#a10-daycountbasis) | reda.041 — `Debt/DayCntBsis` |
| 98 | `accrualStartDate` | Date | Optional | Interest accrual start date | `"2024-03-15"` | reda.041 — `Debt/AcrlStrtDt` |
| 99 | `firstCouponDate` | Date | Optional | First coupon payment date | `"2024-09-15"` | reda.041 — `Debt/FrstCpnDt` |
| 100 | `lastCouponDate` | Date | Optional | Last coupon payment date before maturity | `"2034-03-15"` | reda.041 — `Debt/LastCpnDt` |
| 101 | `businessDayConvention` | Enum | Optional | Business day adjustment rule | See [Appendix A.11](#a11-businessdayconvention) | reda.041 — `Debt/BizDayCnvtn` |
| 102 | `interestComputationMethod` | String | Optional | Interest computation method description | `"30/360"` | reda.041 — `Debt/IntrstCmptnMtd` |

### 6.2 Bond Credit & Seniority

| # | Field Name | Data Type | Required | Description | Allowed Values / Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 103 | `creditRating` | String | Optional | Primary credit rating | `"AA+"` | reda.041 — `Debt/CdtRtg/Rtg` |
| 104 | `ratingAgency` | String | Optional | Rating agency name | `"S&P"`, `"Moody's"`, `"Fitch"` | reda.041 — `Debt/CdtRtg/RtgAgncy` |
| 105 | `seniority` | Enum | Optional | Debt seniority ranking | See [Appendix A.12](#a12-senioritytype) | reda.041 — `Debt/SnrtyTp` |
| 106 | `securedType` | Enum | Optional | Secured/unsecured classification | See [Appendix A.13](#a13-securedtype) | reda.041 — `Debt/ScrdTp` |
| 107 | `guarantor` | String | Optional | Name of guarantor entity (if any) | `"US Government"` | reda.041 — `Debt/Guarntr/Nm` |
| 108 | `covenants` | String | Optional | Summary of key covenants | `"Negative pledge, cross-default"` | reda.041 — `Debt/Cvnts` |
| 109 | `defaultStatus` | Boolean | Optional | Whether the bond is currently in default | `false` | reda.041 — `Debt/DfltSts` |

### 6.3 Bond Call/Put Features

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 110 | `callable` | Boolean | Optional | Whether the bond is callable | `true` | reda.041 — `Debt/CallFeatrs/CallInd` |
| 111 | `callPrice` | Decimal | Conditional | Call price (% of face). Required if `callable` = `true` | `102.00` | reda.041 — `Debt/CallFeatrs/CallPric` |
| 112 | `callDate` | Date | Conditional | First call date. Required if `callable` = `true` | `"2029-03-15"` | reda.041 — `Debt/CallFeatrs/CallDt` |
| 113 | `puttable` | Boolean | Optional | Whether the bond is puttable | `false` | reda.041 — `Debt/PutFeatrs/PutInd` |
| 114 | `putPrice` | Decimal | Conditional | Put price (% of face). Required if `puttable` = `true` | `100.00` | reda.041 — `Debt/PutFeatrs/PutPric` |
| 115 | `putDate` | Date | Conditional | First put date. Required if `puttable` = `true` | `"2029-03-15"` | reda.041 — `Debt/PutFeatrs/PutDt` |

### 6.4 Bond Analytics & Metrics

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 116 | `yieldToMaturity` | Decimal | Optional | Yield to maturity (%) | `5.10` | reda.041 — `Debt/YldToMtrty` |
| 117 | `duration` | Decimal | Optional | Macaulay duration (years) | `7.25` | reda.041 — `Debt/Drtn` |
| 118 | `convexity` | Decimal | Optional | Convexity measure | `62.30` | reda.041 — `Debt/Cnvxty` |
| 119 | `minimumIncrement` | Decimal | Optional | Minimum trading increment | `1000.00` | reda.041 — `TradgDtls/MinIncrmt` |
| 120 | `poolIdentifier` | String | Optional | Pool ID (for securitized bonds, e.g. MBS) | `"POOL-2024-001"` | reda.041 — `Debt/PoolId` |
| 121 | `tranche` | String | Optional | Tranche identifier (for structured bonds) | `"A1"` | reda.041 — `Debt/Trnch` |
| 122 | `series` | String | Optional | Series identifier | `"2024-1"` | reda.041 — `Debt/Srs` |

### 6.5 Bond Additional Data — Credit Analysis

Provide if available. Critical for RM credit risk assessment.

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 123 | `creditRatingMoodys` | String | Optional | Moody's credit rating | `"Aa1"` | reda.041 — `Debt/CdtRtg/Rtg` (Moody's) |
| 124 | `creditRatingSP` | String | Optional | S&P credit rating | `"AA+"` | reda.041 — `Debt/CdtRtg/Rtg` (S&P) |
| 125 | `creditRatingFitch` | String | Optional | Fitch credit rating | `"AA+"` | reda.041 — `Debt/CdtRtg/Rtg` (Fitch) |
| 126 | `creditSpread` | Decimal | Optional | Credit spread over benchmark (basis points) | `85.00` | semt.002 — `FinInstrmDtls/CdtSprd` |
| 127 | `zSpread` | Decimal | Optional | Zero-volatility spread (basis points) | `92.00` | semt.002 — `FinInstrmDtls/ZSprd` |
| 128 | `oas` | Decimal | Optional | Option-adjusted spread (basis points) | `78.00` | semt.002 — `FinInstrmDtls/OptnAdjstdSprd` |
| 129 | `probabilityOfDefault` | Decimal | Optional | Probability of default (%) | `0.05` | — (supplementary) |
| 130 | `lossGivenDefault` | Decimal | Optional | Loss given default (%) | `40.00` | — (supplementary) |
| 131 | `recoveryRate` | Decimal | Optional | Expected recovery rate (%) | `60.00` | — (supplementary) |

### 6.6 Bond Additional Data — Bond Metrics

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 132 | `macaulayDuration` | Decimal | Optional | Macaulay duration (years) | `7.25` | semt.002 — `FinInstrmDtls/Drtn` |
| 133 | `modifiedDuration` | Decimal | Optional | Modified duration | `6.90` | semt.002 — `FinInstrmDtls/ModfdDrtn` |
| 134 | `effectiveDuration` | Decimal | Optional | Effective duration (for bonds with optionality) | `6.50` | semt.002 — `FinInstrmDtls/FctvDrtn` |
| 135 | `dv01` | Decimal | Optional | Dollar value of a basis point (per $1M face) | `690.00` | semt.002 — `FinInstrmDtls/DV01` |
| 136 | `yieldToWorst` | Decimal | Optional | Yield to worst (%) | `4.85` | semt.002 — `FinInstrmDtls/YldToWrst` |
| 137 | `yieldToCall` | Decimal | Optional | Yield to call (%) | `4.90` | semt.002 — `FinInstrmDtls/YldToCall` |
| 138 | `yieldToPut` | Decimal | Optional | Yield to put (%) | `5.00` | semt.002 — `FinInstrmDtls/YldToPut` |
| 139 | `currentYield` | Decimal | Optional | Current yield (%) | `5.25` | semt.002 — `FinInstrmDtls/CurYld` |
| 140 | `accruedInterest` | Decimal | Optional | Accrued interest per unit | `13.15` | semt.002 — `FinInstrmDtls/AcrdIntrst` |

### 6.7 Bond Additional Data — Market Data

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 141 | `bidPrice` | Decimal | Optional | Bid price (% of face value) | `99.75` | semt.002 — `MktPric/BdPric` |
| 142 | `askPrice` | Decimal | Optional | Ask price (% of face value) | `100.25` | semt.002 — `MktPric/AskPric` |
| 143 | `midPrice` | Decimal | Optional | Mid price (% of face value) | `100.00` | semt.002 — `MktPric/MdPric` |
| 144 | `lastTradePrice` | Decimal | Optional | Last traded price (% of face value) | `99.90` | semt.002 — `MktPric/LastTradPric` |
| 145 | `volume` | Decimal | Optional | Trading volume (face value traded) | `50000000.00` | semt.002 — `MktPric/Vol` |
| 146 | `averageDailyVolume` | Decimal | Optional | 30-day average daily volume | `25000000.00` | semt.002 — `MktPric/AvrgDlyVol` |
| 147 | `outstandingAmount` | Decimal | Optional | Total outstanding face value | `500000000.00` | reda.041 — `Debt/OutstndgAmt` |
| 148 | `liquidityScore` | Decimal | Optional | Liquidity score (0–100) | `72.50` | — (supplementary) |

---

## 7. Pricing & Valuation Data

Provided as separate data feeds, linked to securities via `assetId`.

### 7.1 Asset Price (Real-Time / End-of-Day)

| # | Field Name | Data Type | Required | Description | Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 149 | `assetId` | String | Required | Reference to the security record ID | `"SEC-001234"` | semt.003 — `FinInstrmId/OthrId/Id` |
| 150 | `price` | Decimal | Required | Last/closing price | `185.50` | semt.003 — `PricDtls/Pric/Amt` |
| 151 | `currency` | String | Required | Price currency per ISO 4217 | `"USD"` | semt.003 — `PricDtls/Pric/Ccy` |
| 152 | `timestamp` | DateTime | Required | Price timestamp | `"2026-02-08T16:00:00Z"` | semt.003 — `PricDtls/PricDtTm` |
| 153 | `source` | String | Optional | Pricing source | `"Bloomberg"`, `"Reuters"` | semt.003 — `PricDtls/SrcOfPric` |
| 154 | `bidPrice` | Decimal | Optional | Bid price | `185.45` | semt.003 — `PricDtls/BdPric` |
| 155 | `askPrice` | Decimal | Optional | Ask price | `185.55` | semt.003 — `PricDtls/AskPric` |
| 156 | `volume` | Decimal | Optional | Trading volume | `54230000` | semt.003 — `PricDtls/Vol` |

### 7.2 Asset Valuation (Portfolio Valuation)

| # | Field Name | Data Type | Required | Description | Allowed Values / Example | ISO 20022 Reference |
|---|---|---|---|---|---|---|
| 157 | `assetId` | String | Required | Reference to the security record ID | `"SEC-001234"` | semt.002 — `FinInstrmId/OthrId/Id` |
| 158 | `valuationDate` | Date | Required | Valuation date | `"2026-02-08"` | semt.002 — `ValtnDt` |
| 159 | `marketValue` | Decimal | Required | Market value | `1855000.00` | semt.002 — `MktVal/Amt` |
| 160 | `bookValue` | Decimal | Optional | Book/cost value | `1650000.00` | semt.002 — `BookVal/Amt` |
| 161 | `currency` | String | Required | Valuation currency per ISO 4217 | `"USD"` | semt.002 — `MktVal/Ccy` |
| 162 | `valuationMethod` | Enum | Optional | Valuation methodology used | See [Appendix A.14](#a14-valuationmethod) | semt.002 — `ValtnMtd` |
| 163 | `pricingSource` | String | Optional | Source of pricing data | `"Bloomberg"` | semt.002 — `PricgSrc` |

---

## 8. Sample Data

### 8.1 Equity Example (JSON)

```json
{
  "id": "SEC-EQ-001",
  "isin": "US0378331005",
  "cusip": "037833100",
  "ticker": "AAPL",
  "name": "Apple Inc. Common Stock",
  "assetClass": "SECURITIES",
  "securityType": "EQUITY",
  "equityType": "COMMON",
  "issuer": "Apple Inc.",
  "lei": "HWUPKR0MPOU8FGXBT394",
  "fisn": "APPLE INC/SH",
  "cfi": "ESVUFR",
  "currency": "USD",
  "country": "US",
  "exchange": "NASDAQ",
  "mic": "XNAS",
  "tradingStatus": "ACTIVE",
  "formOfSecurity": "BOOK_ENTRY",
  "settlementCycle": "T_PLUS_2",
  "listingDate": "1980-12-12",
  "sector": "Information Technology",
  "industry": "Consumer Electronics",
  "marketCap": 2850000000000.00,
  "sharesOutstanding": 15500000000,
  "dividendYield": 0.55,
  "beta": 1.28,
  "eps": 6.42,
  "peRatio": 29.50,
  "votingRights": true,
  "dividendFrequency": "QUARTERLY",
  "parValue": 0.00001,
  "lotSize": 100,
  "restrictionType": "UNRESTRICTED",
  "paymentStatus": "FULLY_PAID",
  "createdAt": "2026-01-01T00:00:00Z",
  "updatedAt": "2026-02-08T16:00:00Z",
  "additional": {
    "analystRatings": {
      "consensusRating": "BUY",
      "numberOfAnalysts": 42,
      "buyRatings": 30,
      "holdRatings": 10,
      "sellRatings": 2,
      "targetPrice": 210.00
    },
    "financials": {
      "revenue": 394328000000.00,
      "revenueGrowth": 8.10,
      "netIncome": 96995000000.00,
      "eps": 6.42,
      "roe": 160.09,
      "debtToEquity": 1.87
    },
    "valuation": {
      "priceToBook": 47.20,
      "evToEbitda": 22.30,
      "pegRatio": 2.80
    }
  }
}
```

### 8.2 Bond Example (JSON)

```json
{
  "id": "SEC-BD-001",
  "isin": "US912828Z784",
  "cusip": "912828Z78",
  "name": "US Treasury Note 2.5% 2034",
  "assetClass": "SECURITIES",
  "securityType": "BOND",
  "bondType": "GOVERNMENT",
  "issuer": "United States Treasury",
  "lei": "254900HROIFWPRGM1V77",
  "currency": "USD",
  "country": "US",
  "exchange": "OTC",
  "tradingStatus": "ACTIVE",
  "formOfSecurity": "BOOK_ENTRY",
  "settlementCycle": "T_PLUS_1",
  "faceValue": 1000.00,
  "couponRate": 2.50,
  "issueDate": "2024-03-15",
  "maturityDate": "2034-03-15",
  "issuePrice": 99.50,
  "redemptionPrice": 100.00,
  "paymentFrequency": "SEMI_ANNUAL",
  "dayCountBasis": "ACT_ACT",
  "accrualStartDate": "2024-03-15",
  "firstCouponDate": "2024-09-15",
  "businessDayConvention": "MODIFIED_FOLLOWING",
  "seniority": "SENIOR",
  "securedType": "UNSECURED",
  "callable": false,
  "puttable": false,
  "defaultStatus": false,
  "creditRating": "AA+",
  "ratingAgency": "S&P",
  "yieldToMaturity": 2.65,
  "duration": 8.20,
  "convexity": 75.40,
  "createdAt": "2024-03-15T00:00:00Z",
  "updatedAt": "2026-02-08T16:00:00Z",
  "additional": {
    "creditAnalysis": {
      "creditRatingMoodys": "Aaa",
      "creditRatingSP": "AA+",
      "creditRatingFitch": "AAA",
      "creditSpread": 0.00,
      "probabilityOfDefault": 0.001,
      "recoveryRate": 100.00
    },
    "bondMetrics": {
      "macaulayDuration": 8.20,
      "modifiedDuration": 7.95,
      "effectiveDuration": 7.95,
      "dv01": 795.00,
      "yieldToWorst": 2.65,
      "currentYield": 2.50,
      "accruedInterest": 10.27
    },
    "marketData": {
      "bidPrice": 98.75,
      "askPrice": 99.00,
      "midPrice": 98.875,
      "outstandingAmount": 50000000000.00,
      "liquidityScore": 95.00
    }
  }
}
```

---

## Appendix A: Enum Reference Tables

### A.1 SecurityType

ISO 20022: `FinancialInstrumentProductType1Code` — reda.041 — `FinInstrmAttrbts/ClssfctnTp`

| Value | Description |
|---|---|
| `EQUITY` | Common or preferred equity shares |
| `BOND` | Fixed income bond |
| `NOTE` | Promissory or medium-term note |
| `DEBENTURE` | Unsecured debt instrument |
| `PREFERRED_STOCK` | Preferred equity shares |
| `CONVERTIBLE_BOND` | Bond convertible to equity |
| `WARRANT` | Right to purchase shares at a set price |

### A.2 TradingStatus

ISO 20022: `SecurityStatus2Code` — reda.041 — `TradgDtls/TradgSts`

| Value | Description |
|---|---|
| `ACTIVE` | Currently trading |
| `SUSPENDED` | Trading temporarily suspended |
| `HALTED` | Trading halted (regulatory or circuit breaker) |
| `DELISTED` | Removed from exchange listing |
| `PENDING` | Pending listing or approval |

### A.3 FormOfSecurity

ISO 20022: `FormOfSecurity1Code` — reda.041 — `FinInstrmAttrbts/SctiesForm`

| Value | Description |
|---|---|
| `REGISTERED` | Registered in owner's name |
| `BEARER` | Bearer instrument (ownership by possession) |
| `BOOK_ENTRY` | Electronic book-entry form |

### A.4 SettlementCycle

ISO 20022: `SettlementDate1Code` — reda.041 — `TradgDtls/SttlmCycl`

| Value | Description |
|---|---|
| `T_PLUS_0` | Same-day settlement |
| `T_PLUS_1` | Trade date + 1 business day |
| `T_PLUS_2` | Trade date + 2 business days (standard for most equities) |
| `T_PLUS_3` | Trade date + 3 business days |
| `IMMEDIATE` | Immediate settlement |

### A.5 EquityType

ISO 20022: `EquityType1Code` — reda.041 — `Eqty/EqtyTp`

| Value | Description |
|---|---|
| `COMMON` | Common/ordinary shares |
| `PREFERRED` | Preferred shares |
| `DEPOSITARY_RECEIPT` | Depositary receipt (generic) |
| `ADR` | American Depositary Receipt |
| `GDR` | Global Depositary Receipt |
| `WARRANT` | Equity warrant |
| `RIGHT` | Subscription right |
| `UNIT` | Unit (combination of shares and warrants) |

### A.6 PaymentFrequency

ISO 20022: `Frequency1Code` — reda.041 — `Debt/IntrstPmtFrqcy`

| Value | Description |
|---|---|
| `ANNUAL` | Once per year |
| `SEMI_ANNUAL` | Twice per year |
| `QUARTERLY` | Four times per year |
| `MONTHLY` | Once per month |
| `WEEKLY` | Once per week |
| `DAILY` | Daily |
| `AT_MATURITY` | Single payment at maturity |
| `IRREGULAR` | Non-standard schedule |

### A.7 RestrictionType

ISO 20022: `SecurityRestrictionType2Code` — reda.041 — `TradgDtls/RstrctnTp`

| Value | Description |
|---|---|
| `RESTRICTED` | Subject to trading restrictions |
| `UNRESTRICTED` | Freely tradeable |
| `REGULATION_S` | Regulation S (offshore transactions) |
| `RULE_144A` | Rule 144A (qualified institutional buyers) |

### A.8 PaymentStatus

ISO 20022: `SecuritiesPaymentStatus1Code` — reda.041 — `Eqty/PmtSts`

| Value | Description |
|---|---|
| `FULLY_PAID` | Shares are fully paid |
| `PARTLY_PAID` | Shares are partially paid (call liability exists) |
| `NIL_PAID` | No payment made (rights issue) |

### A.9 BondType

ISO 20022: `DebtType1Code` — reda.041 — `Debt/DebtTp`

| Value | Description |
|---|---|
| `GOVERNMENT` | Sovereign/government bond |
| `CORPORATE` | Corporate bond |
| `MUNICIPAL` | Municipal/local government bond |
| `AGENCY` | Government agency bond |
| `CONVERTIBLE` | Convertible bond |
| `ZERO_COUPON` | Zero-coupon bond |
| `FLOATING_RATE` | Floating rate bond |
| `INFLATION_LINKED` | Inflation-linked bond |
| `PERPETUAL` | Perpetual bond (no maturity) |
| `CALLABLE` | Callable bond |
| `PUTTABLE` | Puttable bond |

### A.10 DayCountBasis

ISO 20022: `InterestComputationMethod2Code` — reda.041 — `Debt/DayCntBsis`

| Value | Description |
|---|---|
| `ACT_360` | Actual/360 |
| `ACT_365` | Actual/365 (Fixed) |
| `ACT_ACT` | Actual/Actual (ISDA or ICMA) |
| `THIRTY_360` | 30/360 (US Bond Basis) |
| `THIRTY_E_360` | 30E/360 (Eurobond Basis) |

### A.11 BusinessDayConvention

ISO 20022: `BusinessDayConventionType1Code` — reda.041 — `Debt/BizDayCnvtn`

| Value | Description |
|---|---|
| `FOLLOWING` | Next business day |
| `MODIFIED_FOLLOWING` | Next business day unless it crosses month-end, then preceding |
| `PRECEDING` | Previous business day |
| `MODIFIED_PRECEDING` | Previous business day unless it crosses month-start, then following |
| `UNADJUSTED` | No adjustment |

### A.12 SeniorityType

ISO 20022: `DebtSeniority1Code` — reda.041 — `Debt/SnrtyTp`

| Value | Description |
|---|---|
| `SENIOR` | Senior unsecured debt |
| `SUBORDINATED` | Subordinated debt |
| `JUNIOR` | Junior debt |
| `MEZZANINE` | Mezzanine debt |
| `PARI_PASSU` | Equal ranking with other obligations |

### A.13 SecuredType

ISO 20022: `CollateralType1Code` — reda.041 — `Debt/ScrdTp`

| Value | Description |
|---|---|
| `SECURED` | Backed by specific collateral |
| `UNSECURED` | No specific collateral |
| `PARTIALLY_SECURED` | Partially backed by collateral |

### A.14 ValuationMethod

ISO 20022: `ValuationBasis1Code` — semt.002 — `ValtnMtd`

| Value | Description |
|---|---|
| `MARK_TO_MARKET` | Valued at current market price |
| `MARK_TO_MODEL` | Valued using a pricing model |
| `MARK_TO_MANAGEMENT` | Valued per management estimate |
| `THEORETICAL_PRICING` | Theoretical/calculated price |
| `VENDOR_PRICING` | Price sourced from third-party vendor |

---

## 9. Data Quality Requirements

| Requirement | Description |
|---|---|
| **Completeness** | All required (Required) fields must be populated. Null or empty values for required fields will cause record rejection. |
| **Accuracy** | Identifiers (ISIN, CUSIP, SEDOL, LEI) must be valid and verifiable against official registries. |
| **Timeliness** | End-of-day data must be delivered by **T+0 22:00 UTC**. Intraday pricing updates are preferred. |
| **Consistency** | Enum values must exactly match the allowed values listed in Appendix A (case-sensitive, UPPER_SNAKE_CASE). |
| **Uniqueness** | Each security record must have a unique `id`. Duplicate ISIN records for the same security are not permitted. |
| **Currency Alignment** | All monetary values must be accompanied by their currency code. Currency codes must be valid ISO 4217. |
| **Historical Data** | Initial data load should include at least **3 years** of historical pricing data where available. |

---

## 10. Contact & Support

For questions regarding this specification, please contact:

| Role | Contact |
|---|---|
| **Technical Integration** | it@codefin.io |
| **Data Quality** | kanawat@codefin.io |
| **Business Requirements** | kanawat@codefin.io |

---

*End of Document*
