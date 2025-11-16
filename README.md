End-to-End Azure Weather Pipeline

🌦️ Project Scope

This project is a complete, end-to-end data engineering pipeline built on the Microsoft Azure platform. Its purpose is to automatically ingest 5-day/3-hour weather forecasts from the OpenWeatherMap API, process the data through a medallion architecture, and create analysis-ready tables for reporting and analytics.

The pipeline is fully automated, secure, and built on modern data stack principles.

🛠️ Core Architecture

Orchestration: Azure Data Factory (ADF)

Transformation: Azure Databricks (PySpark & Delta Lake)

Storage: Azure Data Lake Storage (ADLS) Gen2

Security: Azure Key Vault

Workflow & Pipeline

The entire process is orchestrated by a single ADF pipeline (PL_Daily_Weather) that runs on an automated daily trigger.

Get Secrets: The pipeline securely retrieves the OpenWeather API key from Azure Key Vault using the Data Factory's Managed Identity.

Ingest: It calls the OpenWeatherMap API to get the 5-day forecast for Pittsburgh, PA.

Land in Bronze: The raw, unmodified JSON response is saved with a timestamp in the bronze container.

Bronze-to-Silver (Historize): A Databricks notebook is triggered. It reads the raw JSON, cleans and flattens the data, converts units (Kelvin to Fahrenheit), and appends the new 40 forecasts into a historical Delta table. This table stores a complete history of all predictions ever made.

Silver-to-Gold (Analyze): A second Databricks notebook runs. It reads the complete Silver table to create two final Gold tables:

gold_latest_forecast: A snapshot table showing only the single most recent prediction for each future 3-hour block.

gold_daily_summary: An aggregated table showing the daily high/low temps, total rain, and average chance of precipitation for the 5-day outlook.

ADF Pipeline Visual

(Please replace the line below with a screenshot of your pipeline)

``

📚 Project Resources

Notebook 1 (Bronze to Silver): [NB_Bronze_to_Silver_Weather.py](NB_Bronze_to_Silver_Weather.py)

Notebook 2 (Silver to Gold): [NB_Silver_to_Gold_Weather.py](NB_Silver_to_Gold_Weather.py)

Detailed Project Documentation: [View Full Documentation](PROJECT_DOCUMENTATION.md)
