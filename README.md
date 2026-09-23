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
