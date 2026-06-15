# 📧 CRM at Greenweez — Email Campaign Performance Analysis

> **Goal:** Analyze email campaign performance to identify metrics that increase customer engagement and drive conversions.


🔗 **[View Live Google Sheet](https://docs.google.com/spreadsheets/d/1utH2ybYObyBqNPfuwFVq2SDRNkMwiq9OwPKPw1gISUc/edit?gid=759219351#gid=759219351)**

---

## 🧩 Project Overview

Greenweez's CRM team needed to understand how their email campaigns were performing. This project connects raw email send, open, click, and session data to measure key CRM KPIs — and identify which campaigns drive the most revenue and engagement.

---

## 📁 Data Sources

Five raw datasets were imported into Google Sheets using `IMPORTRANGE()`:

| Sheet | Contents |
|---|---|
| **Email** | `journey_id`, `journey_name` — campaign reference list |
| **Sent** | `send_date`, `journey_id`, `sent_nb` — emails sent per campaign |
| **Opening** | `send_date`, `opening_date`, `journey_id`, `opening_nb` — email opens |
| **Click** | `send_date`, `click_date`, `journey_id`, `sendout_id`, `block_name` — clicks per block |
| **Sessions_orders** | `send_date`, `channel_nd`, `journey_id`, `session_id`, `orders_id`, `news`, `turnover` — sessions and orders generated |

---

## 🔧 Data Preparation & Enrichment (CRM_Model)

The **CRM_Model** sheet is the core enrichment layer, built from the Sent data and enriched step by step using multiple `XLOOKUP()` calls:

| Column | Source | Formula |
|---|---|---|
| `journey_name` | Explo_mail | `XLOOKUP(B2, Explo_mail!A:A, Explo_mail!B:B)` |
| `Belgium` | Explo_mail | `IF(REGEXMATCH(B2,"be"),1,0)` |
| `nl` | Explo_mail | `IF(REGEXMATCH(B2,"nl"),1,0)` |
| `join_key` | Concatenation | `=A:A &"_"&B:B` (send_date + journey_id) |
| `nb_openings` | Explo_opening | `XLOOKUP(G2, Explo_opening!G:G, Explo_opening!J:J,"")` |
| `nb_clicks` | Explo_click | `XLOOKUP(G2, Explo_click!I:I, Explo_click!L:L,"")` |
| `nb_sessions` | Explo_sessions_orders | `XLOOKUP(G2, Explo_sessions_orders!I:I, Explo_sessions_orders!L:L,"")` |
| `nb_orders` | Explo_sessions_orders | `XLOOKUP(G2, Explo_sessions_orders!I:I, Explo_sessions_orders!M:M,"")` |
| `turnover` | Explo_sessions_orders | `XLOOKUP(G2, Explo_sessions_orders!I:I, Explo_sessions_orders!N:N,"")` |

A **join_key** (`send_date_journeyid`) was created to uniquely identify each send-campaign combination and enable accurate cross-sheet lookups.

---

## 📐 KPIs Tracked

| KPI | Definition | Purpose |
|---|---|---|
| **Opening Rate** | opening_nb / sent_nb | Shows whether the email subject is compelling enough |
| **Click Rate** | nb_clicks / sent_nb | Measures conversion to click |
| **CTR (Click Through Rate)** | nb_clicks / nb_openings | Measures how engaging the email content is for openers |
| **Turnover per Mille** | turnover / nb_sent × 1,000 | Revenue per 1,000 emails sent — enables fair comparison across campaigns |

---

## 📊 Analysis & Dashboard (CRM_deliverable)

A summary pivot table was built across all campaigns:

| journey_name | sent_nb | nb_openings | nb_clicks | nb_sessions | nb_orders | turnover | opening_rate | click_rate | CTR | Turnover/mille |
|---|---|---|---|---|---|---|---|---|---|---|
| 210801_nl_ete | 95,514 | 22,323 | 1,937 | 787 | 42 | £3,700.60 | 23.37% | 2.03% | 8.68% | £38.74 |
| 210802_nl_menage_A | 47,046 | 11,840 | 1,195 | 428 | 14 | £912.40 | 25.17% | 2.54% | 10.09% | £19.39 |
| 210802_nl_menage_B | 52,272 | 11,643 | 1,183 | 383 | 26 | £1,646.40 | 22.27% | 2.26% | 10.16% | £31.50 |
| 210803_nl_naturel | 39,325 | 8,495 | 961 | 384 | 14 | £744.10 | 21.60% | 2.44% | 11.31% | £18.92 |
| 210805_nl_enfant_1 | 15,307 | 2,708 | 308 | 148 | 9 | £1,023.20 | 17.69% | 2.01% | 11.37% | £66.85 |
| 210805_nl_enfant_2 | 15,151 | 2,682 | 317 | 139 | 10 | £1,101.40 | 17.70% | 2.09% | 11.82% | £72.69 |
| 210805_nl_generale | 77,182 | 20,029 | 2,233 | 926 | 117 | £8,207.70 | 25.95% | 2.89% | 11.15% | £106.34 |
| **Grand Total** | **341,797** | **79,720** | **8,134** | **3,195** | **232** | **£17,335.80** | **23.32%** | **2.38%** | **10.20%** | **£50.72** |

Two charts were built: a line chart showing opening_rate, CTR and Turnover per campaign, and a bar chart showing sent volume per campaign.

---

## 💡 Key Findings

**Overall email performance is average — conversions can be improved**
All campaigns averaged 23.3% open rate, 2.0% click rate, and 10.2% CTR. Many emails that are opened don't convert to sessions or purchases — improving conversion is critical.

**One campaign stands out for revenue efficiency**
The general newsletter (210805_nl_generale) generated £8,207 in revenue with £106 per mille — more than 2x the global average. Its structure and content should be used as a benchmark for future campaigns.

**Smaller targeted campaigns show high efficiency**
enfant_1 and enfant_2 campaigns, despite lower send volumes, achieved over £65 per mille and 11%+ CTR — suggesting that targeted segments are more responsive and content personalization should be expanded.

---

## 🛠️ Tools & Functions Used

| Function/Technique | Usage |
|---|---|
| `IMPORTRANGE()` | Import 5 data sources from external Google Sheets |
| `XLOOKUP()` | Cross-sheet enrichment — bring openings, clicks, sessions, orders into one model |
| `IF(REGEXMATCH())` | Flag Belgium vs NL campaigns based on journey name |
| String concatenation (`&`) | Create join_key combining send_date and journey_id |
| Pivot Table | Aggregate campaign KPIs across all journeys |
| Calculated fields | Opening rate, click rate, CTR, turnover per mille |
| Line chart + Bar chart | Visualize campaign performance and reach |

---
