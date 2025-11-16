# 🌦️ End-to-End Azure Weather Pipeline

This project is an automated weather data pipeline built on **Azure**. It ingests 5-day / 3-hour forecasts from the **OpenWeatherMap API**, lands raw JSON in **Bronze**, processes and historizes records in **Silver**, and generates analytics-ready **Gold** tables using **Delta Lake**.

---

## 🛠️ Architecture

- **ADF** – Orchestration & API ingestion  
- **Databricks (PySpark)** – Transformations  
- **ADLS Gen2** – Storage (Bronze/Silver/Gold)  
- **Key Vault** – Secret management  

---

## 🔄 Pipeline Flow

1. **ADF retrieves API key** from Key Vault  
2. **Ingests weather forecast** and lands raw JSON in **Bronze**  
3. **Databricks Bronze → Silver:** flatten, clean, unit conversions, historization  
4. **Databricks Silver → Gold:**  
   - `gold_latest_forecast` – most recent prediction per 3-hour block  
   - `gold_daily_summary` – daily high/low temps, rainfall, precipitation %  

---

## 📊 ADF Pipeline

![ADF Pipeline](path/to/your-adf-pipeline-image.png)

---

## 📚 Resources

- **Bronze → Silver Notebook:** [`NB_Bronze_to_Silver_Weather.py`](NB_Bronze_to_Silver_Weather.py)  
- **Silver → Gold Notebook:** [`NB_Silver_to_Gold_Weather.py`](NB_Silver_to_Gold_Weather.py)  
- **Full Documentation:** [`PROJECT_DOCUMENTATION.md`](PROJECT_DOCUMENTATION.md)

