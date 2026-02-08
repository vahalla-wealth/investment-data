# Bank Data Preparation Specification

## Securities: Equity & Fixed Income (Bond)

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

## 2. Data Standards & Conventions

### 2.1 Applicable ISO Standards

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

### 2.2 Data Type Definitions

| Data Type | Format | Example |
|---|---|---|
| `String` | UTF-8 text | `"Apple Inc."` |
| `Decimal` | Numeric with decimal precision (up to 18 digits) | `150.2500` |
| `Int` | Integer | `100` |
| `Boolean` | `true` / `false` | `true` |
| `Date` | ISO 8601 date: `YYYY-MM-DD` | `2026-01-15` |
| `DateTime` | ISO 8601 with timezone: `YYYY-MM-DDTHH:MM:SSZ` | `2026-01-15T09:30:00Z` |
| `Enum` | Predefined value from allowed list | `COMMON` |

### 2.3 Field Requirement Levels

| Level | Label | Meaning |
|---|---|---|
| **Required** | Required | Must be provided. Record will be rejected without this field. |
| **Conditionally Required** | Conditional | Required when a specific condition is met (noted in Description). |
| **Optional** | Optional | Provide if available. Enhances data quality. |

### 2.4 Delivery Format

- **Preferred:** JSON or XML (ISO 20022 compliant messages)
- **Acceptable:** CSV with header row matching field names
- **Encoding:** UTF-8
- **Frequency:** Daily end-of-day batch, or real-time via API

---

## 3. Common Security Identification Fields

These fields apply to **all securities** (Equity and Bond). Every record must include these fields.

### 3.1 Primary Identification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 1 | `id` | String | Required | Unique identifier assigned by the bank for this security record | `"SEC-001234"` |
| 2 | `isin` | String | Required | ISIN code per ISO 6166 (12 chars) | `"US0378331005"` |
| 3 | `cusip` | String | Optional | CUSIP identifier (9 chars, US/Canada securities) | `"037833100"` |
| 4 | `sedol` | String | Optional | SEDOL identifier (7 chars, UK/Ireland securities) | `"2046251"` |
| 5 | `ticker` | String | Optional | Exchange ticker symbol | `"AAPL"` |
| 6 | `name` | String | Required | Full legal name of the security | `"Apple Inc. Common Stock"` |
| 7 | `assetClass` | Enum | Required | Must be `SECURITIES` for all records in this specification | `"SECURITIES"` |

### 3.2 Issuer & Classification

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 8 | `issuer` | String | Optional | Name of the issuing entity | `"Apple Inc."` |
| 9 | `lei` | String | Optional | Legal Entity Identifier of issuer per ISO 17442 (20 chars) | `"HWUPKR0MPOU8FGXBT394"` |
| 10 | `fisn` | String | Optional | Financial Instrument Short Name per ISO 18774 (max 35 chars) | `"APPLE INC/SH"` |
| 11 | `cfi` | String | Optional | Classification of Financial Instruments per ISO 10962 (6 chars) | `"ESVUFR"` |
| 12 | `currency` | String | Required | Trading/denomination currency per ISO 4217 (3 chars) | `"USD"` |
| 13 | `country` | String | Optional | Country of issuance per ISO 3166-1 alpha-2 | `"US"` |
| 14 | `description` | String | Optional | Free-text description of the security | `"Common shares of Apple Inc."` |

### 3.3 Security Common Fields

| # | Field Name | Data Type | Required | Description | Allowed Values / Example |
|---|---|---|---|---|---|
| 15 | `securityType` | Enum | Required | Top-level security classification | See [Appendix A.1](#a1-securitytype) |
| 16 | `exchange` | String | Optional | Primary exchange name | `"NASDAQ"` |
| 17 | `mic` | String | Optional | Market Identifier Code per ISO 10383 (4 chars) | `"XNAS"` |
| 18 | `tradingStatus` | Enum | Optional | Current trading status | See [Appendix A.2](#a2-tradingstatus) |
| 19 | `formOfSecurity` | Enum | Optional | Form of security holding | See [Appendix A.3](#a3-formofsecurity) |
| 20 | `settlementCycle` | Enum | Optional | Standard settlement cycle | See [Appendix A.4](#a4-settlementcycle) |
| 21 | `minimumDenomination` | Decimal | Optional | Minimum tradeable denomination | `1000.00` |
| 22 | `listingDate` | Date | Optional | Date the security was listed on the exchange | `"2024-03-15"` |
| 23 | `maturityDate` | Date | Optional | Maturity date (applicable to bonds/notes) | `"2034-03-15"` |

### 3.4 Timestamps

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 24 | `createdAt` | DateTime | Required | Record creation timestamp | `"2026-01-15T09:30:00Z"` |
| 25 | `updatedAt` | DateTime | Required | Last update timestamp | `"2026-02-01T14:00:00Z"` |

---

## 4. Equity Data Fields

For records where `securityType` = `EQUITY`, `PREFERRED_STOCK`, `CONVERTIBLE_BOND`, or `WARRANT`.

### 4.1 Equity-Specific Fields

| # | Field Name | Data Type | Required | Description | Allowed Values / Example |
|---|---|---|---|---|---|
| 26 | `equityType` | Enum | Required | Specific equity sub-type | See [Appendix A.5](#a5-equitytype) |
| 27 | `sector` | String | Optional | GICS or ICB sector classification | `"Information Technology"` |
| 28 | `industry` | String | Optional | Industry sub-classification | `"Consumer Electronics"` |
| 29 | `marketCap` | Decimal | Optional | Market capitalization in trading currency | `2850000000000.00` |
| 30 | `sharesOutstanding` | Decimal | Optional | Total shares outstanding | `15500000000` |
| 31 | `dividendYield` | Decimal | Optional | Annual dividend yield (percentage, e.g. 1.5 = 1.5%) | `0.55` |
| 32 | `beta` | Decimal | Optional | Beta coefficient vs. benchmark | `1.28` |
| 33 | `eps` | Decimal | Optional | Earnings per share (trailing 12 months) | `6.42` |
| 34 | `peRatio` | Decimal | Optional | Price-to-earnings ratio | `29.50` |
| 35 | `votingRights` | Boolean | Optional | Whether shares carry voting rights | `true` |
| 36 | `dividendFrequency` | Enum | Optional | Dividend payment frequency | See [Appendix A.6](#a6-paymentfrequency) |
| 37 | `parValue` | Decimal | Optional | Par/nominal value per share | `0.00001` |
| 38 | `delistingDate` | Date | Optional | Date the security was/will be delisted | `"2026-12-31"` |
| 39 | `lotSize` | Int | Optional | Standard trading lot size | `100` |
| 40 | `boardLot` | Int | Optional | Board lot size (if different from lot size) | `100` |
| 41 | `priceMultiplier` | Decimal | Optional | Contract/price multiplier | `1.00` |
| 42 | `restrictionType` | Enum | Optional | Trading restriction classification | See [Appendix A.7](#a7-restrictiontype) |
| 43 | `paymentStatus` | Enum | Optional | Share payment status | See [Appendix A.8](#a8-paymentstatus) |

### 4.2 Equity Additional Data — Analyst Ratings

Provide if available. Used for RM advisory dashboards.

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 44 | `consensusRating` | String | Optional | Consensus analyst recommendation | `"BUY"`, `"HOLD"`, `"SELL"` |
| 45 | `numberOfAnalysts` | Int | Optional | Number of analysts covering | `42` |
| 46 | `buyRatings` | Int | Optional | Number of buy recommendations | `30` |
| 47 | `holdRatings` | Int | Optional | Number of hold recommendations | `10` |
| 48 | `sellRatings` | Int | Optional | Number of sell recommendations | `2` |
| 49 | `targetPrice` | Decimal | Optional | Consensus target price | `210.00` |
| 50 | `priceTarget12M` | Decimal | Optional | 12-month forward target price | `225.00` |

### 4.3 Equity Additional Data — Financial Metrics

Provide if available. Sourced from latest financial statements.

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 51 | `revenue` | Decimal | Optional | Total revenue (trailing 12 months) | `394328000000.00` |
| 52 | `revenueGrowth` | Decimal | Optional | Year-over-year revenue growth (%) | `8.10` |
| 53 | `netIncome` | Decimal | Optional | Net income (trailing 12 months) | `96995000000.00` |
| 54 | `netIncomeGrowth` | Decimal | Optional | Year-over-year net income growth (%) | `10.20` |
| 55 | `ebitda` | Decimal | Optional | EBITDA | `130541000000.00` |
| 56 | `ebitdaMargin` | Decimal | Optional | EBITDA margin (%) | `33.10` |
| 57 | `operatingMargin` | Decimal | Optional | Operating margin (%) | `30.74` |
| 58 | `profitMargin` | Decimal | Optional | Net profit margin (%) | `24.60` |
| 59 | `roe` | Decimal | Optional | Return on equity (%) | `160.09` |
| 60 | `roa` | Decimal | Optional | Return on assets (%) | `28.30` |
| 61 | `debtToEquity` | Decimal | Optional | Debt-to-equity ratio | `1.87` |
| 62 | `currentRatio` | Decimal | Optional | Current ratio | `0.99` |
| 63 | `quickRatio` | Decimal | Optional | Quick ratio | `0.94` |
| 64 | `freeCashFlow` | Decimal | Optional | Free cash flow | `111443000000.00` |

### 4.4 Equity Additional Data — Valuation Metrics

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 65 | `priceToBook` | Decimal | Optional | Price-to-book ratio | `47.20` |
| 66 | `priceToSales` | Decimal | Optional | Price-to-sales ratio | `7.50` |
| 67 | `evToEbitda` | Decimal | Optional | Enterprise value to EBITDA | `22.30` |
| 68 | `evToSales` | Decimal | Optional | Enterprise value to sales | `7.40` |
| 69 | `pegRatio` | Decimal | Optional | Price/earnings-to-growth ratio | `2.80` |
| 70 | `dividendPayoutRatio` | Decimal | Optional | Dividend payout ratio (%) | `15.40` |
| 71 | `earningsYield` | Decimal | Optional | Earnings yield (%) | `3.39` |

### 4.5 Equity Additional Data — Technical Indicators

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 72 | `rsi` | Decimal | Optional | Relative Strength Index (0–100) | `58.30` |
| 73 | `macd` | Decimal | Optional | MACD value | `2.15` |
| 74 | `sma50` | Decimal | Optional | 50-day Simple Moving Average | `182.50` |
| 75 | `sma200` | Decimal | Optional | 200-day Simple Moving Average | `175.20` |
| 76 | `ema50` | Decimal | Optional | 50-day Exponential Moving Average | `183.10` |
| 77 | `ema200` | Decimal | Optional | 200-day Exponential Moving Average | `176.00` |
| 78 | `bollingerUpper` | Decimal | Optional | Bollinger Band upper | `195.00` |
| 79 | `bollingerLower` | Decimal | Optional | Bollinger Band lower | `170.00` |
| 80 | `atr` | Decimal | Optional | Average True Range | `3.45` |
| 81 | `volume` | Decimal | Optional | Latest trading volume | `54230000` |
| 82 | `averageVolume` | Decimal | Optional | Average daily trading volume (30-day) | `62100000` |

### 4.6 Equity Additional Data — Ownership

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 83 | `institutionalOwnership` | Decimal | Optional | Institutional ownership (%) | `60.50` |
| 84 | `insiderOwnership` | Decimal | Optional | Insider ownership (%) | `0.07` |
| 85 | `publicFloat` | Decimal | Optional | Public float (%) | `99.93` |
| 86 | `sharesShort` | Decimal | Optional | Number of shares sold short | `120000000` |
| 87 | `shortRatio` | Decimal | Optional | Short ratio (days to cover) | `1.90` |
| 88 | `shortPercentOfFloat` | Decimal | Optional | Short interest as % of float | `0.80` |

---

## 5. Bond (Fixed Income) Data Fields

For records where `securityType` = `BOND`, `CONVERTIBLE_BOND`, or `DEBENTURE`.

### 5.1 Bond-Specific Fields

| # | Field Name | Data Type | Required | Description | Allowed Values / Example |
|---|---|---|---|---|---|
| 89 | `bondType` | Enum | Required | Bond sub-type classification | See [Appendix A.9](#a9-bondtype) |
| 90 | `faceValue` | Decimal | Required | Par/face value per unit | `1000.00` |
| 91 | `couponRate` | Decimal | Required | Annual coupon rate (%, e.g. 5.25 = 5.25%) | `5.25` |
| 92 | `issueDate` | Date | Required | Original issuance date | `"2024-03-15"` |
| 93 | `issuePrice` | Decimal | Optional | Price at issuance (% of face value) | `99.50` |
| 94 | `redemptionPrice` | Decimal | Optional | Redemption price (% of face value) | `100.00` |
| 95 | `paymentFrequency` | Enum | Optional | Coupon payment frequency | See [Appendix A.6](#a6-paymentfrequency) |
| 96 | `dayCountBasis` | Enum | Optional | Day count convention for interest calculation | See [Appendix A.10](#a10-daycountbasis) |
| 97 | `accrualStartDate` | Date | Optional | Interest accrual start date | `"2024-03-15"` |
| 98 | `firstCouponDate` | Date | Optional | First coupon payment date | `"2024-09-15"` |
| 99 | `lastCouponDate` | Date | Optional | Last coupon payment date before maturity | `"2034-03-15"` |
| 100 | `businessDayConvention` | Enum | Optional | Business day adjustment rule | See [Appendix A.11](#a11-businessdayconvention) |
| 101 | `interestComputationMethod` | String | Optional | Interest computation method description | `"30/360"` |

### 5.2 Bond Credit & Seniority

| # | Field Name | Data Type | Required | Description | Allowed Values / Example |
|---|---|---|---|---|---|
| 102 | `creditRating` | String | Optional | Primary credit rating | `"AA+"` |
| 103 | `ratingAgency` | String | Optional | Rating agency name | `"S&P"`, `"Moody's"`, `"Fitch"` |
| 104 | `seniority` | Enum | Optional | Debt seniority ranking | See [Appendix A.12](#a12-senioritytype) |
| 105 | `securedType` | Enum | Optional | Secured/unsecured classification | See [Appendix A.13](#a13-securedtype) |
| 106 | `guarantor` | String | Optional | Name of guarantor entity (if any) | `"US Government"` |
| 107 | `covenants` | String | Optional | Summary of key covenants | `"Negative pledge, cross-default"` |
| 108 | `defaultStatus` | Boolean | Optional | Whether the bond is currently in default | `false` |

### 5.3 Bond Call/Put Features

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 109 | `callable` | Boolean | Optional | Whether the bond is callable | `true` |
| 110 | `callPrice` | Decimal | Conditional | Call price (% of face). Required if `callable` = `true` | `102.00` |
| 111 | `callDate` | Date | Conditional | First call date. Required if `callable` = `true` | `"2029-03-15"` |
| 112 | `puttable` | Boolean | Optional | Whether the bond is puttable | `false` |
| 113 | `putPrice` | Decimal | Conditional | Put price (% of face). Required if `puttable` = `true` | `100.00` |
| 114 | `putDate` | Date | Conditional | First put date. Required if `puttable` = `true` | `"2029-03-15"` |

### 5.4 Bond Analytics & Metrics

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 115 | `yieldToMaturity` | Decimal | Optional | Yield to maturity (%) | `5.10` |
| 116 | `duration` | Decimal | Optional | Macaulay duration (years) | `7.25` |
| 117 | `convexity` | Decimal | Optional | Convexity measure | `62.30` |
| 118 | `minimumIncrement` | Decimal | Optional | Minimum trading increment | `1000.00` |
| 119 | `poolIdentifier` | String | Optional | Pool ID (for securitized bonds, e.g. MBS) | `"POOL-2024-001"` |
| 120 | `tranche` | String | Optional | Tranche identifier (for structured bonds) | `"A1"` |
| 121 | `series` | String | Optional | Series identifier | `"2024-1"` |

### 5.5 Bond Additional Data — Credit Analysis

Provide if available. Critical for RM credit risk assessment.

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 122 | `creditRatingMoodys` | String | Optional | Moody's credit rating | `"Aa1"` |
| 123 | `creditRatingSP` | String | Optional | S&P credit rating | `"AA+"` |
| 124 | `creditRatingFitch` | String | Optional | Fitch credit rating | `"AA+"` |
| 125 | `creditSpread` | Decimal | Optional | Credit spread over benchmark (basis points) | `85.00` |
| 126 | `zSpread` | Decimal | Optional | Zero-volatility spread (basis points) | `92.00` |
| 127 | `oas` | Decimal | Optional | Option-adjusted spread (basis points) | `78.00` |
| 128 | `probabilityOfDefault` | Decimal | Optional | Probability of default (%) | `0.05` |
| 129 | `lossGivenDefault` | Decimal | Optional | Loss given default (%) | `40.00` |
| 130 | `recoveryRate` | Decimal | Optional | Expected recovery rate (%) | `60.00` |

### 5.6 Bond Additional Data — Bond Metrics

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 131 | `macaulayDuration` | Decimal | Optional | Macaulay duration (years) | `7.25` |
| 132 | `modifiedDuration` | Decimal | Optional | Modified duration | `6.90` |
| 133 | `effectiveDuration` | Decimal | Optional | Effective duration (for bonds with optionality) | `6.50` |
| 134 | `dv01` | Decimal | Optional | Dollar value of a basis point (per $1M face) | `690.00` |
| 135 | `yieldToWorst` | Decimal | Optional | Yield to worst (%) | `4.85` |
| 136 | `yieldToCall` | Decimal | Optional | Yield to call (%) | `4.90` |
| 137 | `yieldToPut` | Decimal | Optional | Yield to put (%) | `5.00` |
| 138 | `currentYield` | Decimal | Optional | Current yield (%) | `5.25` |
| 139 | `accruedInterest` | Decimal | Optional | Accrued interest per unit | `13.15` |

### 5.7 Bond Additional Data — Market Data

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 140 | `bidPrice` | Decimal | Optional | Bid price (% of face value) | `99.75` |
| 141 | `askPrice` | Decimal | Optional | Ask price (% of face value) | `100.25` |
| 142 | `midPrice` | Decimal | Optional | Mid price (% of face value) | `100.00` |
| 143 | `lastTradePrice` | Decimal | Optional | Last traded price (% of face value) | `99.90` |
| 144 | `volume` | Decimal | Optional | Trading volume (face value traded) | `50000000.00` |
| 145 | `averageDailyVolume` | Decimal | Optional | 30-day average daily volume | `25000000.00` |
| 146 | `outstandingAmount` | Decimal | Optional | Total outstanding face value | `500000000.00` |
| 147 | `liquidityScore` | Decimal | Optional | Liquidity score (0–100) | `72.50` |

---

## 6. Pricing & Valuation Data

Provided as separate data feeds, linked to securities via `assetId`.

### 6.1 Asset Price (Real-Time / End-of-Day)

| # | Field Name | Data Type | Required | Description | Example |
|---|---|---|---|---|---|
| 148 | `assetId` | String | Required | Reference to the security record ID | `"SEC-001234"` |
| 149 | `price` | Decimal | Required | Last/closing price | `185.50` |
| 150 | `currency` | String | Required | Price currency per ISO 4217 | `"USD"` |
| 151 | `timestamp` | DateTime | Required | Price timestamp | `"2026-02-08T16:00:00Z"` |
| 152 | `source` | String | Optional | Pricing source | `"Bloomberg"`, `"Reuters"` |
| 153 | `bidPrice` | Decimal | Optional | Bid price | `185.45` |
| 154 | `askPrice` | Decimal | Optional | Ask price | `185.55` |
| 155 | `volume` | Decimal | Optional | Trading volume | `54230000` |

### 6.2 Asset Valuation (Portfolio Valuation)

| # | Field Name | Data Type | Required | Description | Allowed Values / Example |
|---|---|---|---|---|---|
| 156 | `assetId` | String | Required | Reference to the security record ID | `"SEC-001234"` |
| 157 | `valuationDate` | Date | Required | Valuation date | `"2026-02-08"` |
| 158 | `marketValue` | Decimal | Required | Market value | `1855000.00` |
| 159 | `bookValue` | Decimal | Optional | Book/cost value | `1650000.00` |
| 160 | `currency` | String | Required | Valuation currency per ISO 4217 | `"USD"` |
| 161 | `valuationMethod` | Enum | Optional | Valuation methodology used | See [Appendix A.14](#a14-valuationmethod) |
| 162 | `pricingSource` | String | Optional | Source of pricing data | `"Bloomberg"` |

---

## 7. Sample Data

### 7.1 Equity Example (JSON)

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

### 7.2 Bond Example (JSON)

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

| Value | Description |
|---|---|
| `ACTIVE` | Currently trading |
| `SUSPENDED` | Trading temporarily suspended |
| `HALTED` | Trading halted (regulatory or circuit breaker) |
| `DELISTED` | Removed from exchange listing |
| `PENDING` | Pending listing or approval |

### A.3 FormOfSecurity

| Value | Description |
|---|---|
| `REGISTERED` | Registered in owner's name |
| `BEARER` | Bearer instrument (ownership by possession) |
| `BOOK_ENTRY` | Electronic book-entry form |

### A.4 SettlementCycle

| Value | Description |
|---|---|
| `T_PLUS_0` | Same-day settlement |
| `T_PLUS_1` | Trade date + 1 business day |
| `T_PLUS_2` | Trade date + 2 business days (standard for most equities) |
| `T_PLUS_3` | Trade date + 3 business days |
| `IMMEDIATE` | Immediate settlement |

### A.5 EquityType

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

| Value | Description |
|---|---|
| `RESTRICTED` | Subject to trading restrictions |
| `UNRESTRICTED` | Freely tradeable |
| `REGULATION_S` | Regulation S (offshore transactions) |
| `RULE_144A` | Rule 144A (qualified institutional buyers) |

### A.8 PaymentStatus

| Value | Description |
|---|---|
| `FULLY_PAID` | Shares are fully paid |
| `PARTLY_PAID` | Shares are partially paid (call liability exists) |
| `NIL_PAID` | No payment made (rights issue) |

### A.9 BondType

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

| Value | Description |
|---|---|
| `ACT_360` | Actual/360 |
| `ACT_365` | Actual/365 (Fixed) |
| `ACT_ACT` | Actual/Actual (ISDA or ICMA) |
| `THIRTY_360` | 30/360 (US Bond Basis) |
| `THIRTY_E_360` | 30E/360 (Eurobond Basis) |

### A.11 BusinessDayConvention

| Value | Description |
|---|---|
| `FOLLOWING` | Next business day |
| `MODIFIED_FOLLOWING` | Next business day unless it crosses month-end, then preceding |
| `PRECEDING` | Previous business day |
| `MODIFIED_PRECEDING` | Previous business day unless it crosses month-start, then following |
| `UNADJUSTED` | No adjustment |

### A.12 SeniorityType

| Value | Description |
|---|---|
| `SENIOR` | Senior unsecured debt |
| `SUBORDINATED` | Subordinated debt |
| `JUNIOR` | Junior debt |
| `MEZZANINE` | Mezzanine debt |
| `PARI_PASSU` | Equal ranking with other obligations |

### A.13 SecuredType

| Value | Description |
|---|---|
| `SECURED` | Backed by specific collateral |
| `UNSECURED` | No specific collateral |
| `PARTIALLY_SECURED` | Partially backed by collateral |

### A.14 ValuationMethod

| Value | Description |
|---|---|
| `MARK_TO_MARKET` | Valued at current market price |
| `MARK_TO_MODEL` | Valued using a pricing model |
| `MARK_TO_MANAGEMENT` | Valued per management estimate |
| `THEORETICAL_PRICING` | Theoretical/calculated price |
| `VENDOR_PRICING` | Price sourced from third-party vendor |

---

## 8. Data Quality Requirements

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

## 9. Contact & Support

For questions regarding this specification, please contact:

| Role | Contact |
|---|---|
| **Technical Integration** | _[To be provided]_ |
| **Data Quality** | _[To be provided]_ |
| **Business Requirements** | _[To be provided]_ |

---

*End of Document*
