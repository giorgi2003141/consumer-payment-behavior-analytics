# 🏦 Consumer Payments Analytics Dashboard | Power BI • SQL Server • MCP

An end-to-end banking analytics project that turns **34,000+ consumer payment diary records** into business insights: **who pays, how they pay, and for what**.

Raw survey data was cleaned and modeled in **SQL Server (T-SQL)**, loaded into **Power BI** as a star schema, and analyzed with **16 DAX measures**. Data-type tuning, model cleanup, and DAX authoring were partly automated through an **MCP Server integration with Claude AI**.

![Dashboard Overview](screenshots/overview.png)

---

📌 At a Glance

| Metric | Value |
|---|---|
| Total transactions | **34,243** (30,399 payments + 3,844 non-payment records) |
| Total payment value | **$4.94M** |
| Average payment | **$162** |
| Consumers surveyed | **6,080** (5,531 with recorded transactions) |
| Digital share of payment value | **80.1%** |
| Cash share of payment value | **4.2%** |
| Mobile payment adoption | **71.4%** of consumers |
| Online shopping | **86.5%** of consumers |
| Payment methods used per consumer | **2.2** on average |
| Diary period | Sep 29 – Nov 17, 2025 |

---

📊 Key Insights

1. Value and volume tell different stories
| Payment category | Payment value | Transactions | Avg. payment |
|---|---|---|---|
| 🏦 Bank Transfer | **$2.31M** (47%) | 4,066 | $569 |
| 💳 Card | $1.49M (30%) | **19,526** (64%) | $76 |
| 📄 Paper (check) | $519K | 801 | $648 |
| 💵 Cash | $208K | 4,160 | $50 |
| 📱 Mobile | $151K | 1,123 | $134 |

Bank transfers are **rare but large**. Cards are **frequent and mid-sized**. Cash is **frequent but small**.

### 2. Payment method follows spending purpose
| Spending type | Dominant method | Share of category value |
|---|---|---|
| Everyday purchases | 💳 Card | Essential Retail 88%, Transportation 86%, Food & Dining 83%, Retail 78%, Healthcare 74% |
| Bills & obligations | 🏦 Bank Transfer | Financial Services 82%, Government 48% |
| Person-to-person | 📱 Mobile | Top method for P2P |
| Donations | 📄 Paper check | Top method for donations |

Store purchases go on a card, bills go by bank transfer, and payments to friends go by phone. This pattern is useful for product and partnership strategy.

### 3. Payment value grows with age
| Age group | Payment value | Avg. payment |
|---|---|---|
| 18–24 | $40K | $63 |
| 25–34 | $405K | $117 |
| 35–44 | $819K | $143 |
| 45–54 | $906K | $163 |
| 55–64 | $945K | $148 |
| **65+** | **$1.82M** | **$212** |

Consumers aged 65+ generate **37%** of total payment value and have the highest average ticket.

### 4. Value is highly concentrated (Pareto)
- The **top 10% of consumers** generate **63%** of total payment value.
- Payments of **$1K or more** make up only **3%** of transactions but **55%** of value.
- Payments under **$25** are **42%** of transactions but only **3%** of value.

### 5. Where the money goes
Financial Services (**$2.02M**) and Housing & Utilities (**$948K**) account for **60%** of payment value. Food & Dining has the most transactions (**5,946**) but only $158K in value.

---

## 🗂️ Data Model (Star Schema)

```
                     DimDate
                        │
   DimConsumer ── FactTransaction ── DimMerchant
                     │        │
          DimPaymentMethod   DimDevice
```

All relationships are many-to-one with single-direction filtering from the dimensions to the fact table.

| Table | Grain / content |
|---|---|
| `FactTransaction` | One row per diary record: amount, payment flag, date/consumer/merchant/method/device keys, amount band |
| `DimConsumer` | Age and age group, gender, education, income band, mobile/online adoption flags, digital engagement score and segment, value decile, number of payment methods used |
| `DimDate` | Calendar attributes: year, month, day of week, weekend flag |
| `DimMerchant` | Merchant name and merchant group (spending category) |
| `DimPaymentMethod` | Payment method, payment category, digital flag |
| `DimDevice` | Device name, device category, digital flag |

### Modeling decisions
- `Amount` is stored as **Fixed Decimal** to avoid floating-point rounding errors in totals.
- Key and code columns are set to **Summarize By = None**.
- Month and weekday names are sorted by their numeric columns.
- Calculated columns: `Amount Band`, `Consumer Value Decile`, `Methods Used`, `Method Diversity`.
- All measures live in a dedicated `_Measures` table.

---

## 🧮 DAX Measures

| Measure | Definition |
|---|---|
| Total Transactions | `COUNTROWS(FactTransaction)` |
| Payment Transactions | Transactions where `PaymentFlag = TRUE` |
| Non-Payment Transactions | Transactions where `PaymentFlag = FALSE` |
| Total Payment Value | Sum of `Amount` for payment transactions |
| Average Payment Value | Total Payment Value ÷ Payment Transactions |
| Payment Rate % | Payment Transactions ÷ Total Transactions |
| Unique Consumers | Distinct consumers with transactions |
| Consumers | All surveyed consumers |
| % Digital Payments | Share of value paid with digital methods |
| Cash Share | Share of value paid in cash |
| Mobile Adoption Rate | Share of consumers who use mobile payments |
| Mobile P2P Rate | Share of consumers who send mobile person-to-person payments |
| Mobile In-Person Rate | Share of consumers who pay by mobile in stores |
| Online Shopping Rate | Share of consumers who shop online |
| Avg Digital Engagement | Average digital engagement score |
| Avg Methods per Consumer | Average number of payment methods used |

Example:
```dax
% Digital Payments =
DIVIDE(
    CALCULATE([Total Payment Value], 'analytics DimPaymentMethod'[IsDigital] = TRUE()),
    [Total Payment Value],
    0
)
```

---

## 🛠️ Tools Used

**SQL Server (T-SQL)** · **Power BI Desktop** · **Power Query** · **DAX** · **MCP Server** · **Claude AI**

---

## 📁 Repository Structure

```
├── dashboard/      → bank-project.pbix
├── sql/            → T-SQL scripts (cleaning & star schema)
├── screenshots/    → dashboard page images
└── README.md
```




---

**Data source:** [dataset name and link]

👤 **Author:** [Your Name] · [LinkedIn](https://linkedin.com/in/your-profile)

Feedback is always welcome!
