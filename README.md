# Industrial Equipment & Procurement Analytics Platform
An end-to-end data pipeline, relational lakehouse model, and business intelligence reporting solution tracking procurement performance, component lead times, and inventory fulfillment.

[![Microsoft Fabric](https://img.shields.io/badge/Platform-Microsoft%20Fabric-blue?style=flat-square&logo=microsoft)](https://learn.microsoft.com/en-us/fabric/)
[![Power BI](https://img.shields.io/badge/BI-Power%20BI%20Desktop-yellow?style=flat-square&logo=powerbi)](https://powerbi.microsoft.com/)
[![Python](https://img.shields.io/badge/ETL-Python%20%7C%20Pandas-3776AB?style=flat-square&logo=python)](https://pandas.pydata.org/)
[![SQL](https://img.shields.io/badge/Query-T--SQL-CC292B?style=flat-square&logo=microsoftsqlserver)](https://www.microsoft.com/sql-server)

---

## 📌 Project Overview & Problem Statement

In industrial engineering and fabrication projects, procurement dispatches and Bill of Materials (BOM) tracking are frequently managed via disconnected spreadsheets. This causes:
* Siloed data across project bills of materials, delivery orders, and vendor schedules.
* Delayed visibility into critical-path component shortages (e.g., control panels, transmitters, switchgear).
* Extensive manual overhead reconciling delivery statuses against master schedules.

**Objective:** Build an automated end-to-end data pipeline transforming multi-source procurement exports into an analytical Lakehouse architecture, delivering real-time visibility into vendor reliability, order fulfillment, and critical component lead times.

---

## 🏗️ Architecture & Data Flow

The project applies the **Medallion Lakehouse Architecture**:

[ Raw Multi-Source Data ]
(BOM Extracts, Vendor Dispatches, Warehouse Logs)
│
▼
[ Bronze Layer (Raw Ingestion) ]
• Untouched source files stored in Parquet/Delta format
│
▼ (ETL / Pandas & SQL Standardization)
[ Silver Layer (Cleaned & Standardized) ]
• Schema enforcement, deduplication, timestamp formatting
• Modbus & Tag ID relational consistency checks
│
▼ (Dimensional Modeling / Star Schema)
[ Gold Layer (Curated Semantic Model) ]
• 1 Central Fact Table (Fact_Procurement_Orders)
• 4 Dimension Tables (Vendors, Equipment_Master, Project_Sites, Calendar)
│
▼
[ Power BI Analytics & Executive Dashboard ]

---

## 📊 Dimensional Model (Star Schema)

The semantic model is designed in a strict **Star Schema** to optimize query performance and ensure DAX filter context integrity:

      ┌─────────────────────┐
      │     Dim_Calendar    │
      └──────────┬──────────┘
                 │ (1:N)
                 ▼

                 * **`Fact_Procurement_Orders`**: Detailed transaction records (Order ID, PO Number, Order Date, Delivery Date, Unit Price, Quantity Ordered, Quantity Received, Defect Flag).
* **`Dim_Equipment_Master`**: Component classifications, tag prefixes, specification codes, and sub-system categories.
* **`Dim_Vendors`**: Supplier name, tier category, country of origin, and contracted lead times.
* **`Dim_Project_Sites`**: Delivery destinations, package codes, and assigned site engineers.
* **`Dim_Calendar`**: Continuous date table supporting time-intelligence analytics.

---

## ⚙️ Technical Implementation

### 1. Data Cleaning & Transformation (Python / Pandas)
* Standardized inconsistent part numbers, serial codes, and vendor strings using regular expressions.
* Resolved `NULL` delivery dates and created dynamic operational flags for backorders.
* Handled data type conversions, ensuring decimal consistency for financial values and standard ISO timestamps.

```python
# Sample transformation snippet: Calculating fulfillment status and lead time variance
import pandas as pd

def transform_procurement_data(df_raw: pd.DataFrame) -> pd.DataFrame:
    df = df_raw.copy()
    df.columns = df.columns.str.strip().str.lower().str.replace(' ', '_')
    
    # Lead time calculation
    df['order_date'] = pd.to_datetime(df['order_date'])
    df['actual_delivery_date'] = pd.to_datetime(df['actual_delivery_date'])
    df['actual_lead_time_days'] = (df['actual_delivery_date'] - df['order_date']).dt.days
    
    # Fulfillment categorization
    df['fulfillment_status'] = df.apply(
        lambda row: 'Complete' if row['qty_received'] >= row['qty_ordered']
        else ('Partial' if row['qty_received'] > 0 else 'Pending'),
        axis=1
    )
    return df
```
2. Core Analytical DAX Measures

// Measure 1: On-Time In-Full (OTIF) Delivery %
OTIF Delivery % = 
DIVIDE(
    CALCULATE(
        COUNTROWS(Fact_Procurement_Orders),
        Fact_Procurement_Orders[Actual_Lead_Time_Days] <= Fact_Procurement_Orders[Contracted_Lead_Time_Days],
        Fact_Procurement_Orders[Fulfillment_Status] = "Complete"
    ),
    COUNTROWS(Fact_Procurement_Orders),
    0
)

// Measure 2: Material Shortage Exposure ($)
Open Shortage Value = 
SUMX(
    FILTER(
        Fact_Procurement_Orders,
        Fact_Procurement_Orders[Fulfillment_Status] IN {"Pending", "Partial"}
    ),
    (Fact_Procurement_Orders[Qty_Ordered] - Fact_Procurement_Orders[Qty_Received]) 
    * Fact_Procurement_Orders[Unit_Cost]
)

📈 Dashboard Features & Insights
Page 1: Operational Procurement & Logistics Control

Core KPI strip: Overall OTIF %, Active Shortage Cost, Average Lead Time Deviation.

Interactive component tracker: Slicing by package, subsystem, and delivery status.

Page 2: Vendor Reliability & Risk Analysis

Vendor scorecard ranking on-time dispatch rate and defect percentage.

Lead time variance heat map flagging systemic supplier delays before site mobilization.

🛠️ Repository Structure
Plaintext
├── data/
│   ├── raw/                  # Source CSV/Excel exports (Bronze simulation)
│   └── processed/            # Cleaned dimensional tables (Silver/Gold)
├── scripts/
│   ├── data_pipeline.py      # Python ETL pipeline script
│   └── schema_queries.sql    # T-SQL table definitions and views
├── powerbi/
│   └── procurement_analytics_report.pbix
├── images/
│   ├── data_model_schema.png # Star Schema relationship screenshot
│   └── dashboard_preview.png # Power BI report visuals
└── README.md
🚀 How to Run / Reproduce
Clone the repository:

Bash
git clone [https://github.com/your-username/industrial-procurement-analytics.git](https://github.com/your-username/industrial-procurement-analytics.git)
cd industrial-procurement-analytics
Set up Python environment & run ETL:

Bash
pip install pandas numpy openpyxl
python scripts/data_pipeline.py
Open the Report:

Open powerbi/procurement_analytics_report.pbix using Power BI Desktop.

Update the local data source directory to point to your cloned data/processed/ folder.

Click Refresh.

👤 Author & Contact
Name: Muhammad Shafiq

Background: Electrical & Automation Engineer transitioning to Analytics Engineering

LinkedIn: linkedin.com/in/shafiqafizudin

Email: [shafiq.afizudin@outlook.com]
