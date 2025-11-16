# azure-weather-pipeline

End-to-End Weather Forecast Pipeline on Azure
This project is an end-to-end data engineering pipeline that ingests 5-day/3-hour weather forecasts from the OpenWeatherMap API, processes the data through a medallion architecture, and creates aggregated analysis tables.

The pipeline is built entirely on the Azure cloud, using Azure Data Factory for orchestration, Azure Key Vault for security, Azure Data Lake Storage Gen2 for storage, and Azure Databricks for all transformations.

🏛️ Project Architecture
The project follows a medallion architecture to progressively refine raw data into high-quality, analysis-ready tables.

Orchestration: Azure Data Factory (ADF)

Storage: Azure Data Lake Storage (ADLS) Gen2

Bronze: Raw, immutable JSON data from the API.

Silver: Cleaned, structured, and historical forecast data (one row per forecast per city).

Gold: Aggregated, business-ready tables for analysis.

Transformation: Azure Databricks (PySpark)

Security: Azure Key Vault (to store all API keys, secrets, and connection strings).
