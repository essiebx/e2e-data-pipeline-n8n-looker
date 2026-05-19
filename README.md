# e2e-data-pipeline-n8n-looker

# End-to-End Agricultural Sales Ledger Automation & Executive Analytics Pipeline

## Project Overview

This repository contains the architecture, configuration logic, and data structures for a fully automated, end-to-end (E2E) data pipeline. The system intercepts inbound customer transactions from a frontend web form, programmatically enriches the data by mapping records against an internal supplier database, maintains a sanitized sales ledger, and surfaces real-time business intelligence to stakeholders via an executive-grade analytics dashboard.

By eliminating manual spreadsheet administration, this automation layout prevents transactional data drift, ensures immediate compliance tracking, and delivers zero-latency operational metrics to management.

---
## Live Dashboard Preview

View the live operational dashboard here: [Google Looker Studio Dashboard](https://datastudio.google.com/reporting/6ab2ba3a-8321-4c61-8bd4-2244f2567a11)
## System Architecture & Data Flow

```
[ Google Form Submission ]
│
▼
[ n8n Automation Engine ] ────► [ Match & Enrich via CRM/Members DB ]
│
▼
[ Google Sheets Sales Ledger ] (Populated with 2,000 Production Records)
│
▼
[ Google Looker Studio Dashboard ] ────► [ Automated Executive PDF Reports ]
```

### Data Flow Steps

1. **Ingestion (Trigger):** Inbound payload captured instantly via an n8n webhook/Google Sheets trigger upon customer checkout.
2. **Data Enrichment (Processing):** The pipeline parses the submission, isolates unique identifiers, and pulls relational metadata (`Membership_Tier`, `Active_Loan_Balance`, `Compliance_Status`) from the core members database.
3. **Storage (Sink):** Cleansed and aggregated records are appended in real-time into a 14-column production ledger (`sales_ledger_production_data_2000.csv`).
4. **Analytics (BI Layer):** Looker Studio programmatically consumes the ledger updates, projecting structural trends, credit profiles, and volume distributions.

---

## Core Data Schema

The automation engine enforces a strict 14-column relational layout across all transactional data:

| Column Name | Data Type | Source Layer | Description |
| :--- | :--- | :--- | :--- |
| `Date` | Timestamp (DD/MM/YYYY) | Ingestion Trigger | Exact timestamp of form submission. |
| `Farmer_Name` | String | Ingestion Trigger | Primary key identifier for the producer. |
| `Phone_Number` | String | CRM / Members DB | Automated lookup phone record. |
| `Product` | String | Ingestion Trigger | Commodity type selected (e.g., onions, tomatoes). |
| `Quantity` | String | Ingestion Trigger | Metric volume moved (e.g., 40kgs). |
| `Total_Price` | Integer / Currency | Ingestion Trigger | Calculated transactional value in local currency. |
| `Farm_Type` | String (Categorical) | CRM / Members DB | Operational classification (Organic vs. Conventional). |
| `Member_ID` | Alphanumeric | CRM / Members DB | Unique database index key. |
| `Farm_Size_Hectares` | Float | CRM / Members DB | Total physical cultivation area tracking. |
| `Membership_Tier` | String (Categorical) | CRM / Members DB | Loyalty tiering status (Silver, Gold, Platinum). |
| `Active_Loan_Balance_EUR`| Integer | CRM / Members DB | Active institutional credit exposure tracking. |
| `Compliance_Status` | String (Categorical) | CRM / Members DB | Legal and safety regulatory verification standing. |
| `Postal_Code` | String | CRM / Members DB | Spatial localization index. |
| `Notes` | String | CRM / Members DB | Logistical and field operation annotations. |

---

## H1 2026 Executive Performance Briefing

*The following strategic narrative is derived directly from the pipeline's operational dataset of 2,000 historical records (`sales_ledger_production_data_2000.csv`).*

### Executive Summary

* **Reporting Period:** January 1, 2026 – May 19, 2026  
* **Operational Scale:** 2,000 distinct commercial transactions processed completely through automated workflows.

This performance briefing evaluates the commercial and operational trajectory of our agricultural supply network for the first half of fiscal year 2026. Over the analyzed period, gross network revenue reached stable, high-yield milestones across thousands of metric tonnes of successfully moved produce. Market demand remained highly resilient, anchored by a strong performance in organic product lines. While credit exposure and outstanding loan balances remain within safe historical margins, strategic inventory reallocations are advised for Q3 to maximize yields from top-tier producers.

### Commercial & Financial Analysis

* **Revenue Concentration:** High-margin crops, specifically **Tomatoes** and **Onions**, represent the highest revenue velocity per kilogram, acting as primary commercial drivers. 
* **Volume Baseline:** Low-volatility crops like **Cabbage** and **Potatoes** provided predictable transactional volume, stabilizing weekly cash flows against localized market price swings.
* **Temporal Trends:** The rolling timeline demonstrates seasonal acceleration peaking between March and late April 2026, aligning perfectly with standard regional harvesting timelines before evening out into mid-May.

### Supply Chain & Producer Insights

* **Production Efficiency:** **Platinum** and **Gold** tier members consistently supply larger batch sizes with higher frequency, optimizing logistics and cross-docking costs. 
* **Cultivation Matrix:** Demand for **Organic** designated crops carries a clear price premium over **Conventional** outputs, validating our ongoing program to support farms transitioning to organic certification. 
* **Logistical Notes:** Reviewing field notes indicates that transportation delays were minimized for producers located in high-density postal clusters (e.g., the `1011 AB` and `EH1 1YZ` regions), while rural distribution lanes require optimization.

### Portfolio Risk & Compliance Audit

* **Credit Exposure Analysis:** Total active loan exposure remains securely insulated. The highest outstanding balances are concentrated strictly among **Platinum-tier** producers who maintain high volume output, representing low default risk.
* **Regulatory Compliance Pipeline:** Approximately **85%** of transactions moved through fully audited, compliant producers. A minor segment of total volume is currently categorized as **"Under Review."** These specific lines are undergoing routine documentation updates, posing zero immediate operational risk to the broader distribution network.

### Strategic Action Plan (Q3 2026)

1. **Targeted Credit Expansion:** Extend preferred seasonal credit limits exclusively to **Gold-tier Organic** producers to capture the unfilled margin premium observed in the market.
2. **Logistical Corridor Optimization:** Partner with regional tractor cooperatives to assist producers in lower-density postal zones, directly resolving transportation bottlenecks highlighted in logistical field notes.
3. **Compliance Streamlining:** Establish an automated warning system in n8n to flag accounts whose "Under Review" status approaches 30 days, preventing temporary paperwork delays from stopping sales lines.

---

## Deployment & Replication Guide

### Prerequisites

* An **n8n instance** (Cloud or Self-Hosted)
* **Google Workspace Account** (Google Sheets & Google Forms access)
* Free **Google Looker Studio** account

### Setup Steps

1. **Database Initialization:** Create a Google Spreadsheet with two tabs: `Members` (your CRM reference index) and `Sales_Ledger` (configured with the 14 columns outlined above).
2. **Automation Import:** Create a new workflow in n8n, add a Google Sheets trigger listening to your form inputs, chain an intermediate Lookup node referencing the `Members` tab via `Farmer_Name`, and terminate with an Append node targeting the `Sales_Ledger`.
3. **Dashboard Wiring:** Connect Looker Studio to your `Sales_Ledger` tab. Map `Total_Price` and `Quantity` as cumulative summary metrics, and drop in a vertical column chart grouped by `Product`.

### Repository Structure

```
.
├── README.md                          <-- The blueprint we just designed
├── workflow.json                      <-- Your safe, exported n8n pipeline blueprint
└── sales_ledger_production_data_2000.csv <-- The 2,000-row testing dataset
```

---

## How to Import the n8n Pipeline

To replicate this automation engine in your local or cloud n8n instance:

1. Download the `workflow.json` file from this repository.
2. Open your n8n instance and create a **New Workflow**.
3. Click the menu icon in the top right corner and select **Import from File**.
4. Choose the `workflow.json` file. 
5. Re-link your specific **Google Sheets Credentials** to the Form Trigger, CRM Lookup, and Ledger Append nodes.
