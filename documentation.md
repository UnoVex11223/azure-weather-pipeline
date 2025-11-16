# Project: End-to-End Azure Weather Pipeline

This document outlines the end-to-end architecture and implementation of an automated data pipeline. The project ingests 5-day weather forecasts from the OpenWeatherMap API, processes the data through a medallion architecture, and creates analysis-ready tables in a secure, automated, and scalable manner using Azure cloud services.

---

**Step 1: Infrastructure Provisioning**

A resource group named `weather-pipeline-rg` was created in the East US region. Inside this group:

- An Azure Data Lake Storage Gen2 account `weatherpipelinedih` was created with hierarchical namespace enabled.
- Three storage containers were created to support the medallion architecture: `bronze`, `silver`, and `gold`.
- Azure Data Factory instance `weather-pipeline-adf-ih` was created.
- Azure Databricks workspace `weather-pipeline-dbw` was provisioned.
- Azure Key Vault `w-pipeline-kv-ihughes` was created to store secrets.

---

**Step 2: Security & Service Configuration**

- A free OpenWeatherMap API Key was generated and stored in Key Vault as `weather-api-key`.
- The ADLS storage account key was stored as `storage-key`.
- A Databricks PAT token was created and stored as `databricks-pat`.
- ADF’s Managed Identity was given **Key Vault Secrets User** to read secrets.
- The user account was assigned **Key Vault Secrets Officer**.
- ADF linked services were configured:
  - `ls_key_vault` for Key Vault (Managed Identity)
  - `ls_adls_gen2` for ADLS (key pulled from Key Vault)
  - `ls_databricks` for Databricks (PAT pulled from Key Vault)
- A Databricks secret scope `kv-scope` was created to securely access Key Vault secrets in notebooks.

---

**Step 3: Data Transformation (Bronze to Silver)**

Performed by notebook `NB_Bronze_to_Silver_Weather`:

- Connects to ADLS by reading `storage-key` from the Databricks secret scope.
- Defines a StructType schema for nested OpenWeatherMap JSON.
- Reads the latest raw JSON file from the bronze container.
- Converts timestamps into proper formats.
- Converts Kelvin temperature fields to Fahrenheit using a UDF.
- Builds a cleaned DataFrame and removes unnecessary columns.
- Writes to the `silver_weather_forecast` Delta table:
  - Creates the table on first run.
  - Appends 40 new forecast records on all subsequent runs.

---

**Step 4: Data Transformation (Silver to Gold)**

Performed by notebook `NB_Silver_to_Gold_Weather`:

- Reads the full historical Silver Delta table.

Creates two Gold tables:

1. `gold_latest_forecast`  
   - Uses a window function to select the most recent forecast per 3-hour block.  
   - Filters out stale forecasts.

2. `gold_daily_summary`  
   - Groups by date to calculate high/low temps, total rainfall, and average precipitation probability.  
   - Uses `round()` and `coalesce()` for clean numeric output.

---

**Step 5: Pipeline Orchestration**

ADF pipeline `PL_Daily_Weather` orchestrates the full flow:

1. Get API key from Key Vault.  
2. Call OpenWeatherMap API to retrieve forecast JSON.  
3. Copy the API response to the bronze container with a timestamped filename.  
4. Execute the Bronze to Silver Databricks notebook.  
5. Execute the Silver to Gold Databricks notebook.

---

**Step 6: Automation & Scheduling**

A daily trigger named `TR_Daily_Weather` was created:

- Runs once per day at 6:00 AM Eastern Time.
- The pipeline was published, enabling full automation.


