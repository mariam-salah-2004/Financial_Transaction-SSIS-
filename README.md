# SSIS Financial ETL Pipeline

## Project Overview
This repository contains a robust End-to-End ETL (Extract, Transform, Load) pipeline built using **SQL Server Integration Services (SSIS)**. The project focuses on migrating financial transaction data from multiple source formats (Excel, Flat Files) into a structured Data Warehouse while maintaining high data integrity and applying complex business logic.

## Architecture & Workflow

### 1. Control Flow
The workflow is designed to ensure a clean data state before processing:
- **Truncate Warehouse Tables:** Execute SQL Tasks to clear the `suppliers_warehouse`, `exchange_rate_warehouse`, and `financial_data_warehouse` tables to prevent data duplication.
- **Parallel Data Flows:** Independent data flow tasks for Suppliers and Exchange Rates run concurrently to optimize performance.

### 2. Data Flow & Transformations
The core logic resides within the `Customer_Transaction` Data Flow task:
- **Multi-Source Extraction:** Ingesting data from OLE DB Sources, Excel, and CSV files.
- **Data Conversion:** Standardizing data types across various sources to ensure compatibility with the SQL Server destination.
- **Lookup Transformation (Exchange Rates):**
  - Performs a lookup against the `Exchange_Rate` table.
  - **Handling No Matches:** Transactions without a matching exchange rate are routed to a **Derived Column** transformation where a default `Exchange_Rate = 1.0` (USD Base) is applied.
- **Union All:** Merges the matched and default-valued streams back into a single pipeline.
- **Business Logic (Derived Columns):** Calculates the total amount in USD using the formula: `Amount_USD = amount * exchange_rate`.
- **Lookup Transformation (Suppliers):** Validates supplier IDs against the master supplier list.
- **Conditional Split (Data Quality):**
  - **Matched Records:** Loaded directly into the `Dest Financial Transaction` destination.
  - **Unmatched Records:** Routed to a `Missing_Suppliers` flat file for manual auditing and data cleaning.

## Tech Stack
- **ETL Tool:** SQL Server Integration Services (SSIS)
- **Database:** Microsoft SQL Server (T-SQL)
- **Sources:** Excel (.xlsx), Flat Files (.csv, .txt)

## Key Features
- **Data Integrity:** Clear separation between source landing and warehouse loading via Truncate-Load patterns.
- **Automated Error Routing:** Captures missing master data (Suppliers) without breaking the pipeline execution, enabling efficient data auditing.
- **Business Logic Implementation:** Handles missing exchange rates by applying business-defined defaults (1.0) to ensure consistent financial reporting.

<img width="1073" height="480" alt="Screenshot 2026-05-10 011028" src="https://github.com/user-attachments/assets/8f3defdc-891d-417b-94b1-727cb58e469e" />
<img width="347" height="355" alt="Screenshot 2026-05-10 011055" src="https://github.com/user-attachments/assets/fd55a2c0-be79-4144-a238-54c0a7334d1a" />
<img width="435" height="375" alt="Screenshot 2026-05-10 011102" src="https://github.com/user-attachments/assets/e4d99d51-a1fb-41c5-a5fa-c616adaf56e9" />
<img width="746" height="596" alt="Screenshot 2026-05-10 011224" src="https://github.com/user-attachments/assets/4635a34d-bea0-4c8e-863e-812cb2ed5e51" />



