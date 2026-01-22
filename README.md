
<p align="center">
<img width="809" height="446" alt="Screenshot (104)" src="https://github.com/user-attachments/assets/a3ae0ac3-ede1-4843-91ad-be44ec9d56c1" />

</p>

<h1 align="center">🚖 Cab Trips Analytics Platform</h1>
<h3 align="center">End-to-End Databricks Lakehouse with Declarative Pipelines (DLT/LakeFlow)</h3>

<p align="center">
  <img src="https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white" alt="Databricks"/>
  <img src="https://img.shields.io/badge/Apache_Spark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white" alt="Apache Spark"/>
  <img src="https://img.shields.io/badge/Delta_Lake-00ADD8?style=for-the-badge&logo=delta&logoColor=white" alt="Delta Lake"/>
  <img src="https://img.shields.io/badge/AWS_S3-569A31?style=for-the-badge&logo=amazons3&logoColor=white" alt="AWS S3"/>
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Pipeline-Declarative%20(DLT%2FLakeFlow)-blue?style=flat-square" alt="Pipeline"/>
  <img src="https://img.shields.io/badge/Architecture-Medallion-gold?style=flat-square" alt="Architecture"/>
  <img src="https://img.shields.io/badge/Records-500K+-green?style=flat-square" alt="Records"/>
  <img src="https://img.shields.io/badge/CDC-Enabled-orange?style=flat-square" alt="CDC"/>
  <img src="https://img.shields.io/badge/RBAC-Implemented-purple?style=flat-square" alt="RBAC"/>
</p>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Business Problem](#-business-problem)
- [Solution Architecture](#-solution-architecture)
- [Key Features](#-key-features)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Data Pipeline Layers](#-data-pipeline-layers)
  - [Bronze Layer](#-bronze-layer-raw-ingestion)
  - [Silver Layer](#-silver-layer-cleansed--enriched)
  - [Gold Layer](#-gold-layer-business-ready-analytics)
- [Data Model](#-data-model)
- [Security & Access Control](#-security--access-control-rbac)
- [Pipeline Configuration](#-pipeline-configuration)
- [Setup & Deployment](#-setup--deployment)
- [Analytics & Dashboards](#-analytics--dashboards)
- [Future Enhancements](#-future-enhancements)
- [Author](#-author)

---

## 🎯 Overview

A production-grade **Databricks Lakehouse** analytics platform that processes **500K+ cab trip records** using **Declarative Pipelines (Delta Live Tables / LakeFlow)** with a robust **Medallion Architecture (Bronze → Silver → Gold)**. The pipeline supports both **batch and streaming ingestion** via **Auto Loader**, implements **Change Data Capture (CDC)** with **SCD Type 1 & 2** patterns, and delivers **business-ready fact views** with **role-based access control (RBAC)** for city-level data isolation.

<img width="1536" height="1024" alt="ChatGPT Image Jan 18, 2026, 08_19_32 PM" src="https://github.com/user-attachments/assets/8134d695-bc12-4049-a93f-4c35e17dee04" />

### 🔑 What Makes This Project Stand Out

| Capability | Implementation |
|------------|----------------|
| **Real-time Streaming** | Auto Loader with cloudFiles for continuous ingestion |
| **Data Quality** | Schema inference, evolution, and corrupt record handling |
| **Change Tracking** | Delta Change Data Feed (CDF) for full CDC support |
| **Data Governance** | RBAC with city-specific secure views |
| **Performance** | Serverless compute with auto-optimization |
| **Analytics** | Databricks Genie + Amazon QuickSight integration |

---

## 💼 Business Problem

A cab transportation company operates across **10+ cities in India** and generates massive volumes of trip data daily. The business faces several challenges:

### Challenges Addressed

1. **Data Silos**: Trip data scattered across multiple sources with no unified view
2. **Delayed Insights**: Batch-only processing causing stale analytics
3. **Data Quality Issues**: Inconsistent schemas, missing values, and duplicate records
4. **Access Control**: No proper data isolation between city managers
5. **Scalability**: Manual pipelines unable to handle growing data volumes
6. **Historical Tracking**: No mechanism to track changes over time (CDC/SCD)

### Solution Value Proposition

| Metric | Improvement |
|--------|-------------|
| Data Freshness | Batch → **Near Real-time** (minutes) |
| Pipeline Reliability | Manual → **Automated with Auto Loader** |
| Data Governance | None → **RBAC with city-level isolation** |
| Query Performance | Hours → **Seconds** (materialized views) |
| Schema Management | Manual → **Auto inference + evolution** |

---

## 🏗 Solution Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                           DATABRICKS LAKEHOUSE ARCHITECTURE                              │
├─────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                         │
│   ┌──────────────┐      ┌──────────────────────────────────────────────────────────┐   │
│   │              │      │                  MEDALLION ARCHITECTURE                   │   │
│   │   AWS S3     │      │  ┌─────────┐    ┌─────────┐    ┌─────────┐              │   │
│   │   Bucket     │─────▶│  │ BRONZE  │───▶│ SILVER  │───▶│  GOLD   │              │   │
│   │              │      │  │  Layer  │    │  Layer  │    │  Layer  │              │   │
│   │  ┌────────┐  │      │  └────┬────┘    └────┬────┘    └────┬────┘              │   │
│   │  │ /city  │  │      │       │              │              │                    │   │
│   │  └────────┘  │      │   Raw Data      Cleaned &      Business                 │   │
│   │  ┌────────┐  │      │   Ingestion     Enriched       Ready Views              │   │
│   │  │ /trips │  │      │                                                          │   │
│   │  └────────┘  │      └──────────────────────────────────────────────────────────┘   │
│   └──────────────┘                                                                      │
│          │                                                                              │
│          │ Auto Loader                                                                  │
│          │ (cloudFiles)     ┌────────────────────────────────────────────────────────┐ │
│          │                  │              DATABRICKS WORKSPACE                       │ │
│          ▼                  │  ┌──────────────────────────────────────────────────┐  │ │
│   ┌──────────────┐          │  │         Declarative Pipeline (DLT/LakeFlow)      │  │ │
│   │              │          │  │                                                  │  │ │
│   │  Streaming   │          │  │  • Materialized Views    • Delta Optimization   │  │ │
│   │  Ingestion   │─────────▶│  │  • Streaming Tables      • Change Data Feed     │  │ │
│   │              │          │  │  • Schema Evolution      • Auto Compaction      │  │ │
│   │  + Batch     │          │  │                                                  │  │ │
│   │  Full Load   │          │  └──────────────────────────────────────────────────┘  │ │
│   └──────────────┘          │                                                         │ │
│                             │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐ │ │
│                             │  │Unity Catalog│  │   Genie     │  │ Serverless      │ │ │
│                             │  │  + RBAC     │  │  Analytics  │  │ Compute         │ │ │
│                             │  └─────────────┘  └─────────────┘  └─────────────────┘ │ │
│                             └────────────────────────────────────────────────────────┘ │
│                                                      │                                 │
│                                                      ▼                                 │
│                             ┌────────────────────────────────────────────────────────┐ │
│                             │              CONSUMPTION LAYER                          │ │
│                             │  ┌─────────────────┐  ┌─────────────────────────────┐  │ │
│                             │  │ Amazon          │  │ City-Specific Secure Views  │  │ │
│                             │  │ QuickSight      │  │ (RBAC Enabled)              │  │ │
│                             │  │ Dashboards      │  │ • fact_trips_chandigarh    │  │ │
│                             │  │                 │  │ • fact_trips_coimbatore    │  │ │
│                             │  │                 │  │ • fact_trips_jaipur        │  │ │
│                             │  └─────────────────┘  └─────────────────────────────┘  │ │
│                             └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────────────────┘
```

### Architecture Components

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Data Source** | AWS S3 | Raw CSV file storage |
| **Ingestion** | Auto Loader (cloudFiles) | Streaming + batch ingestion |
| **Processing** | Databricks Declarative Pipeline | ETL transformations |
| **Storage** | Delta Lake | ACID-compliant data lakehouse |
| **Compute** | Serverless Cluster | Managed, auto-scaling compute |
| **Governance** | Unity Catalog + RBAC | Access control & lineage |
| **Analytics** | Databricks Genie | Conversational analytics |
| **Visualization** | Amazon QuickSight | Business dashboards |

---

## ✨ Key Features

### Data Engineering Capabilities

| Feature | Description |
|---------|-------------|
| **Declarative Pipelines** | DLT/LakeFlow for simplified ETL development |
| **Auto Loader** | Incremental file ingestion with exactly-once semantics |
| **Schema Evolution** | Automatic schema inference and rescue mode |
| **CDC/SCD** | Change Data Capture with SCD Type 1 & 2 support |
| **Materialized Views** | Pre-computed aggregations for fast queries |
| **Streaming Tables** | Real-time data processing capabilities |

### Data Quality & Governance

| Feature | Description |
|---------|-------------|
| **Change Data Feed** | Delta CDF enabled for tracking row-level changes |
| **Auto Optimization** | Automatic file compaction and optimize writes |
| **Corrupt Record Handling** | PERMISSIVE mode with `_corrupt_record` column |
| **Metadata Tracking** | File path and ingestion timestamp captured |
| **RBAC Implementation** | City-level data isolation with secure views |

---

## 🛠 Technology Stack

<table>
<tr>
<td>

### Core Platform
- **Databricks Lakehouse**
- **Delta Lake**
- **Apache Spark**
- **PySpark**

</td>
<td>

### Pipeline & Compute
- **Declarative Pipelines (DLT/LakeFlow)**
- **Auto Loader (cloudFiles)**
- **Serverless Cluster**
- **Unity Catalog**

</td>
<td>

### Cloud & Analytics
- **AWS S3**
- **Amazon QuickSight**
- **Databricks Genie**
- **API Key Authentication**

</td>
</tr>
</table>

### Technical Specifications

```yaml
Pipeline Configuration:
  type: Declarative (DLT/LakeFlow)
  mode: Triggered / Continuous
  compute: Serverless Cluster
  
Delta Table Properties:
  delta.enableChangeDataFeed: "true"
  delta.autoOptimize.optimizeWrite: "true"
  delta.autoOptimize.autoCompact: "true"

Auto Loader Settings:
  format: cloudFiles
  cloudFiles.format: csv
  cloudFiles.inferColumnTypes: "true"
  cloudFiles.schemaEvolutionMode: rescue
  cloudFiles.maxFilesPerTrigger: 100
```

---

## 📁 Project Structure

```
Project_Transportation/
│
├── 📂 Transportation_Pipeline/
│   │
│   ├── 📂 transformations/
│   │   │
│   │   ├── 📂 Bronze/                    # Raw data ingestion layer
│   │   │   ├── 🐍 city.py                # City dimension (batch)
│   │   │   └── 🐍 trips.py               # Trips fact (streaming)
│   │   │
│   │   ├── 📂 Silver/                    # Cleansed & enriched layer
│   │   │   ├── 🐍 calendar.py            # Calendar dimension
│   │   │   └── 🐍 trips.py               # Enriched trips with joins
│   │   │
│   │   └── 📂 Gold/                      # Business-ready layer
│   │       ├── 📜 trips_gold.sql         # Master fact view
│   │       ├── 📜 trips_chandigarh.sql   # City-specific view
│   │       ├── 📜 trips_coimbatore.sql   # City-specific view
│   │       ├── 📜 trips_jaipur.sql       # City-specific view
│   │       ├── 📜 trips_indore.sql       # City-specific view
│   │       ├── 📜 trips_kochi.sql        # City-specific view
│   │       ├── 📜 trips_lucknow.sql      # City-specific view
│   │       ├── 📜 trips_mysore.sql       # City-specific view
│   │       ├── 📜 trips_surat.sql        # City-specific view
│   │       ├── 📜 trips_vadodara.sql     # City-specific view
│   │       └── 📜 trips_visakhapatnam.sql# City-specific view
│   │
│   └── 📜 pipeline_config.json           # DLT/LakeFlow configuration
│
├── 📂 project_setup/                      # Initial setup notebook
│
├── 📂 docs/                              # Documentation
│   └── 📂 architecture/                  # Architecture diagrams
│
└── 📄 README.md                          # This file
```

---

## 🔄 Data Pipeline Layers

<img width="1536" height="1024" alt="ChatGPT Image Jan 18, 2026, 08_22_55 PM" src="https://github.com/user-attachments/assets/7b08b96d-5a80-4045-afe7-745d794add40" />

### 🟤 Bronze Layer (Raw Ingestion)

The Bronze layer captures raw data with minimal transformations, preserving the original state for auditability.

#### Design Principles
- **Raw data preservation** with metadata enrichment
- **Schema inference** with corrupt record handling
- **Incremental processing** via Auto Loader
- **Delta optimization** enabled for performance

---

#### Bronze City Table (Batch Materialized View)

```python
from pyspark import pipelines as dp
from pyspark.sql.functions import col, current_timestamp
from pyspark.sql.functions import md5, concat_ws, sha2

# Configuration
SOURCE_PATH = "s3://goodcabs-project-raw-data/data-store/city"

@dp.materialized_view(
    name="transportation.bronze.city",
    comment="City Raw Data Processing",
    table_properties={
        "quality": "bronze",
        "layer": "bronze",
        "source_format": "csv",
        "delta.enableChangeDataFeed": "true",
        "delta.autoOptimize.optimizeWrite": "true",
        "delta.autoOptimize.autoCompact": "true"
    }
)
def city_bronze():
    df = (
        spark.read.format("csv")
        .option("header", "true")
        .option("inferSchema", "true")
        .option("mode", "PERMISSIVE")
        .option("mergeSchema", "true")
        .option("columnNameOfCorruptRecord", "_corrupt_record")
        .load(SOURCE_PATH)
    )
    
    # Add metadata columns for auditing
    df = (
        df.withColumn("file_name", col("_metadata.file_path"))
          .withColumn("ingest_datetime", current_timestamp())
    )
    
    return df
```

**Key Features:**
| Feature | Implementation |
|---------|----------------|
| `inferSchema` | Automatic data type detection |
| `PERMISSIVE` mode | Continues processing despite errors |
| `mergeSchema` | Handles schema evolution |
| `_corrupt_record` | Captures malformed rows |
| `_metadata.file_path` | Source file tracking |
| Change Data Feed | Enabled for CDC tracking |

---

#### Bronze Trips Table (Streaming Auto Loader)

```python
from pyspark import pipelines as dp
import pyspark.sql.functions as F

# Configuration
SOURCE_PATH = "s3://goodcabs-project-raw-data/data-store/trips"

@dp.table(
    name="transportation.bronze.trips",
    comment="Streaming ingestion of raw trips data with Auto Loader",
    table_properties={
        "quality": "bronze",
        "layer": "bronze",
        "source_format": "csv",
        "delta.enableChangeDataFeed": "true",
        "delta.autoOptimize.optimizeWrite": "true",
        "delta.autoOptimize.autoCompact": "true",
    }
)
def trips_bronze():
    df = (
        spark.readStream.format("cloudFiles")
        .option("cloudFiles.format", "csv")
        .option("cloudFiles.inferColumnTypes", "true")
        .option("cloudFiles.schemaEvolutionMode", "rescue")
        .option("cloudFiles.maxFilesPerTrigger", 100)
        .load(SOURCE_PATH)
    )
    
    # Handle problematic column names
    df = df.withColumnRenamed("distance_travelled(km)", "distance_travelled_km")
    
    # Add metadata columns
    df = (
        df.withColumn("file_name", F.col("_metadata.file_path"))
          .withColumn("ingest_datetime", F.current_timestamp())
    )
    
    return df
```

**Auto Loader Configuration:**
| Option | Value | Purpose |
|--------|-------|---------|
| `cloudFiles.format` | csv | Source file format |
| `cloudFiles.inferColumnTypes` | true | Auto-detect data types |
| `cloudFiles.schemaEvolutionMode` | rescue | Handle new columns in `_rescued_data` |
| `cloudFiles.maxFilesPerTrigger` | 100 | Batch size control |

---

### ⚪ Silver Layer (Cleansed & Enriched)

The Silver layer transforms raw data into clean, standardized, and enriched datasets ready for analytics.

#### Design Principles
- **Data cleansing** and standardization
- **Dimensional modeling** with fact-dimension joins
- **Business logic** implementation
- **Reusable dimensions** for reporting

---

#### Calendar Dimension (Materialized View)

```python
from pyspark import pipelines as dp
from pyspark.sql import functions as F

# Configuration from pipeline parameters
start_date = spark.conf.get("start_date")
end_date = spark.conf.get("end_date")

@dp.materialized_view(
    name="transportation.silver.calendar",
    comment="Calendar dimension with comprehensive date attributes and Indian holidays",
    table_properties={
        "quality": "silver",
        "layer": "silver",
        "delta.enableChangeDataFeed": "true",
        "delta.autoOptimize.optimizeWrite": "true",
        "delta.autoOptimize.autoCompact": "true",
    },
)
def calendar():
    # Generate date sequence
    df = spark.sql(f"""
        SELECT explode(sequence(
            to_date('{start_date}'),
            to_date('{end_date}'),
            interval 1 day
        )) as date
    """)
    
    # Date key for dimensional modeling
    df = df.withColumn(
        "date_key", 
        F.date_format(F.col("date"), "yyyyMMdd").cast("int")
    )
    
    # Time hierarchy attributes
    df = (
        df.withColumn("year", F.year(F.col("date")))
          .withColumn("month", F.month(F.col("date")))
          .withColumn("quarter", F.quarter(F.col("date")))
    )
    
    # Day attributes
    df = (
        df.withColumn("day_of_month", F.dayofmonth(F.col("date")))
          .withColumn("day_of_week", F.date_format(F.col("date"), "EEEE"))
          .withColumn("day_of_week_abbr", F.date_format(F.col("date"), "EEE"))
          .withColumn("day_of_week_num", F.dayofweek(F.col("date")))
    )
    
    # Month and quarter labels
    df = (
        df.withColumn("month_name", F.date_format(F.col("date"), "MMMM"))
          .withColumn(
              "month_year",
              F.concat(F.date_format(F.col("date"), "MMMM"), F.lit(" "), F.col("year"))
          )
          .withColumn(
              "quarter_year",
              F.concat(F.lit("Q"), F.col("quarter"), F.lit(" "), F.col("year"))
          )
    )
    
    # Week and day of year
    df = (
        df.withColumn("week_of_year", F.weekofyear(F.col("date")))
          .withColumn("day_of_year", F.dayofyear(F.col("date")))
    )
    
    # Weekend/weekday flags
    df = (
        df.withColumn(
            "is_weekend",
            F.when(F.col("day_of_week_num").isin([1, 7]), True).otherwise(False)
        )
        .withColumn(
            "is_weekday",
            F.when(F.col("day_of_week_num").isin([1, 7]), False).otherwise(True)
        )
    )
    
    # Indian national holidays
    df = (
        df.withColumn(
            "holiday_name",
            F.when(
                (F.col("month") == 1) & (F.col("day_of_month") == 26),
                F.lit("Republic Day")
            )
            .when(
                (F.col("month") == 8) & (F.col("day_of_month") == 15),
                F.lit("Independence Day")
            )
            .when(
                (F.col("month") == 10) & (F.col("day_of_month") == 2),
                F.lit("Gandhi Jayanti")
            )
            .otherwise(None)
        )
        .withColumn(
            "is_holiday",
            F.when(F.col("holiday_name").isNotNull(), True).otherwise(False)
        )
    )
    
    # Processing timestamp
    df = df.withColumn("silver_processed_timestamp", F.current_timestamp())
    
    # Select final columns
    df_silver = df.select(
        "date", "date_key", "year", "month", "day_of_month",
        "day_of_week", "day_of_week_abbr", "month_name", "month_year",
        "quarter", "quarter_year", "week_of_year", "day_of_year",
        "is_weekday", "is_weekend", "is_holiday", "holiday_name",
        "silver_processed_timestamp"
    )
    
    return df_silver
```

**Calendar Dimension Attributes:**
| Category | Attributes |
|----------|------------|
| **Date Keys** | date, date_key |
| **Hierarchy** | year, quarter, month, week_of_year, day_of_year |
| **Labels** | month_name, month_year, quarter_year, day_of_week |
| **Flags** | is_weekend, is_weekday, is_holiday |
| **Holidays** | Republic Day, Independence Day, Gandhi Jayanti |

---

### 🟡 Gold Layer (Business-Ready Analytics)

<img width="1920" height="1080" alt="Screenshot (101)" src="https://github.com/user-attachments/assets/a7c04fd3-3c9d-4b03-a045-52726307c33a" />

The Gold layer delivers pre-aggregated, business-ready data models optimized for analytics and reporting.

#### Design Principles
- **Star schema** fact-dimension modeling
- **Pre-computed joins** for query performance
- **City-level isolation** with secure views
- **Self-service analytics** ready

---

#### Master Fact View (fact_trips)

```sql
CREATE OR REPLACE VIEW transportation.gold.fact_trips AS (
    SELECT 
        -- Trip identifiers
        t.id,
        t.business_date,
        
        -- City dimension
        t.city_id,
        c.city_name,
        
        -- Trip metrics
        t.passenger_category,
        t.distance_kms,
        t.sales_amt,
        t.passenger_rating,
        t.driver_rating,
        
        -- Calendar dimension attributes
        ca.month,
        ca.day_of_month,
        ca.day_of_week,
        ca.month_name,
        ca.month_year,
        ca.quarter,
        ca.quarter_year,
        ca.week_of_year,
        ca.is_weekday,
        ca.is_weekend,
        ca.is_holiday AS national_holiday
        
    FROM transportation.silver.trips t
    
    -- Join with City dimension
    JOIN transportation.silver.city c 
        ON t.city_id = c.city_id
    
    -- Join with Calendar dimension
    JOIN transportation.silver.calendar ca 
        ON t.business_date = ca.date
);
```

**Fact Table Metrics:**
| Metric | Description | Use Case |
|--------|-------------|----------|
| `distance_kms` | Trip distance | Route analysis |
| `sales_amt` | Revenue | Financial reporting |
| `passenger_rating` | Customer satisfaction | Service quality |
| `driver_rating` | Driver performance | HR analytics |

---

#### City-Specific Secure Views (RBAC)

Each city manager gets access only to their city's data through filtered views.

```sql
-- Chandigarh City View
CREATE OR REPLACE VIEW transportation.gold.fact_trips_chandigarh AS (
    SELECT *
    FROM transportation.gold.fact_trips
    WHERE city_id = 'CH01'
);

-- Coimbatore City View
CREATE OR REPLACE VIEW transportation.gold.fact_trips_coimbatore AS (
    SELECT *
    FROM transportation.gold.fact_trips
    WHERE city_id = 'TN01'
);

-- Jaipur City View
CREATE OR REPLACE VIEW transportation.gold.fact_trips_jaipur AS (
    SELECT *
    FROM transportation.gold.fact_trips
    WHERE city_id = 'RJ01'
);

-- Additional cities: Indore, Kochi, Lucknow, Mysore, Surat, Vadodara, Visakhapatnam
```

**City Code Mapping:**
| City | Code | State |
|------|------|-------|
| Chandigarh | CH01 | Chandigarh |
| Coimbatore | TN01 | Tamil Nadu |
| Jaipur | RJ01 | Rajasthan |
| Indore | MP01 | Madhya Pradesh |
| Kochi | KL01 | Kerala |
| Lucknow | UP01 | Uttar Pradesh |
| Mysore | KA01 | Karnataka |
| Surat | GJ01 | Gujarat |
| Vadodara | GJ02 | Gujarat |
| Visakhapatnam | AP01 | Andhra Pradesh |

---

## 📊 Data Model

### Star Schema Design

```
                           ┌─────────────────────┐
                           │   dim_calendar      │
                           │─────────────────────│
                           │ PK: date            │
                           │    date_key         │
                           │    year             │
                           │    quarter          │
                           │    month            │
                           │    day_of_week      │
                           │    is_weekend       │
                           │    is_holiday       │
                           │    holiday_name     │
                           └──────────┬──────────┘
                                      │
                                      │ business_date = date
                                      │
┌─────────────────────┐              ▼              ┌─────────────────────┐
│     dim_city        │    ┌─────────────────────┐  │                     │
│─────────────────────│    │    fact_trips       │  │  City-Specific      │
│ PK: city_id         │───▶│─────────────────────│◀─│  Secure Views       │
│    city_name        │    │ FK: city_id         │  │                     │
│                     │    │ FK: business_date   │  │  • fact_trips_      │
└─────────────────────┘    │    id               │  │    chandigarh       │
                           │    passenger_cat    │  │  • fact_trips_      │
                           │    distance_kms     │  │    coimbatore       │
                           │    sales_amt        │  │  • fact_trips_      │
                           │    passenger_rating │  │    jaipur           │
                           │    driver_rating    │  │  • ... (10 cities)  │
                           └─────────────────────┘  └─────────────────────┘
```

### Dataset Details

<img width="1920" height="1080" alt="Screenshot (103)" src="https://github.com/user-attachments/assets/4f870082-eb92-4631-b94f-fc74b34480f8" />

#### Source: AWS S3

| Path | Content | Format |
|------|---------|--------|
| `s3://goodcabs-project-raw-data/data-store/city/` | City master data | CSV |
| `s3://goodcabs-project-raw-data/data-store/trips/` | Trip transactions | CSV |

#### Trips Dataset Schema

| Column | Type | Description |
|--------|------|-------------|
| `id` | STRING | Unique trip identifier |
| `business_date` | DATE | Trip date |
| `city_id` | STRING | City reference |
| `passenger_category` | STRING | Customer segment |
| `distance_travelled_km` | DOUBLE | Trip distance |
| `sales_amt` | DOUBLE | Trip revenue |
| `passenger_rating` | DOUBLE | Rating by passenger |
| `driver_rating` | DOUBLE | Rating to driver |

#### City Dataset Schema

| Column | Type | Description |
|--------|------|-------------|
| `city_id` | STRING | Primary key |
| `city_name` | STRING | City display name |

---

## 🔐 Security & Access Control (RBAC)

### Implementation Strategy

```
┌─────────────────────────────────────────────────────────────────┐
│                    RBAC IMPLEMENTATION                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────┐                                          │
│   │  Analytics      │────▶ ALL city data access               │
│   │  Admin Group    │      (fact_trips)                        │
│   └─────────────────┘                                          │
│                                                                 │
│   ┌─────────────────┐                                          │
│   │ city_mgr_       │────▶ fact_trips_chandigarh ONLY         │
│   │ chandigarh      │                                          │
│   └─────────────────┘                                          │
│                                                                 │
│   ┌─────────────────┐                                          │
│   │ city_mgr_       │────▶ fact_trips_coimbatore ONLY         │
│   │ coimbatore      │                                          │
│   └─────────────────┘                                          │
│                                                                 │
│   ┌─────────────────┐                                          │
│   │ city_mgr_       │────▶ fact_trips_jaipur ONLY             │
│   │ jaipur          │                                          │
│   └─────────────────┘                                          │
│                                                                 │
│   ... (Additional city groups)                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Permission Matrix

| Role | Master Fact View | City Views | Actions |
|------|------------------|------------|---------|
| **Analytics Admin** | ✅ Full Access | ✅ All Cities | SELECT, MODIFY |
| **City Manager** | ❌ No Access | ✅ Own City Only | SELECT |
| **Data Engineer** | ✅ Full Access | ✅ All Cities | SELECT, MODIFY, CREATE |

### Group Configuration

```sql
-- Create groups
CREATE GROUP analytics_admin;
CREATE GROUP city_mgr_chandigarh;
CREATE GROUP city_mgr_coimbatore;
-- ... additional groups

-- Grant permissions
GRANT SELECT ON transportation.gold.fact_trips TO analytics_admin;
GRANT SELECT ON transportation.gold.fact_trips_chandigarh TO city_mgr_chandigarh;
GRANT SELECT ON transportation.gold.fact_trips_coimbatore TO city_mgr_coimbatore;
```

---

## ⚙️ Pipeline Configuration

### Pipeline Settings

```json
{
  "name": "Transportation_Pipeline",
  "catalog": "transportation",
  "target": "bronze",
  "configuration": {
    "start_date": "2025-01-01",
    "end_date": "2025-12-31"
  },
  "clusters": [
    {
      "label": "default",
      "mode": "SERVERLESS"
    }
  ],
  "development": false,
  "continuous": false,
  "channel": "CURRENT",
  "edition": "ADVANCED"
}
```

### Execution Modes

| Mode | Trigger | Use Case |
|------|---------|----------|
| **Triggered** | Manual or scheduled | Batch processing |
| **Continuous** | Always running | Real-time streaming |

### Delta Table Properties

```python
table_properties = {
    "quality": "bronze/silver/gold",
    "layer": "bronze/silver/gold",
    "source_format": "csv",
    "delta.enableChangeDataFeed": "true",      # CDC support
    "delta.autoOptimize.optimizeWrite": "true", # Auto-optimize writes
    "delta.autoOptimize.autoCompact": "true"    # Auto file compaction
}
```

---

## 🚀 Setup & Deployment

### Prerequisites

- Databricks Workspace with Unity Catalog enabled
- AWS Account with S3 access
- Serverless compute enabled
- Amazon QuickSight (optional for dashboards)

### Step 1: Upload Raw Data to S3

```bash
# Create bucket structure
aws s3 mb s3://goodcabs-project-raw-data

# Upload data files
aws s3 cp city.csv s3://goodcabs-project-raw-data/data-store/city/
aws s3 cp trips_2025-01-01.csv s3://goodcabs-project-raw-data/data-store/trips/
```

### Step 2: Configure Databricks Connection

1. Create an **External Location** in Unity Catalog pointing to S3
2. Generate **API Key** for authentication
3. Set up **IAM Role** with S3 read permissions

### Step 3: Deploy Pipeline

```python
# Using Databricks CLI
databricks pipelines create --json-file pipeline_config.json

# Or through UI
# Navigate to: Jobs & Pipelines > Create Pipeline
```

### Step 4: Configure Parameters

```json
{
  "start_date": "2025-01-01",
  "end_date": "2025-12-31"
}
```

### Step 5: Run Pipeline

```bash
# Trigger pipeline run
databricks pipelines start --pipeline-id <PIPELINE_ID>

# Enable continuous mode (optional)
databricks pipelines update --pipeline-id <PIPELINE_ID> --continuous true
```

---

## 📈 Analytics & Dashboards

### Databricks Genie

Conversational analytics for natural language queries:

```
User: "What is the total revenue for Chandigarh in Q3 2025?"
Genie: Returns aggregated sales_amt for CH01 where quarter = 3
```

### Amazon QuickSight Integration

Connect Gold views to QuickSight for interactive dashboards:

| Dashboard | Metrics |
|-----------|---------|
| **Executive Summary** | Total trips, revenue, avg ratings |
| **City Performance** | City-wise comparison |
| **Time Analysis** | Weekday vs weekend patterns |
| **Driver Analytics** | Rating distribution, performance |

### Sample Analytics Queries

```sql
-- Monthly revenue by city
SELECT 
    city_name,
    month_name,
    SUM(sales_amt) AS total_revenue,
    COUNT(*) AS trip_count
FROM transportation.gold.fact_trips
GROUP BY city_name, month_name
ORDER BY total_revenue DESC;

-- Weekend vs Weekday analysis
SELECT 
    CASE WHEN is_weekend THEN 'Weekend' ELSE 'Weekday' END AS day_type,
    AVG(distance_kms) AS avg_distance,
    AVG(passenger_rating) AS avg_rating
FROM transportation.gold.fact_trips
GROUP BY is_weekend;

-- Holiday impact analysis
SELECT 
    holiday_name,
    COUNT(*) AS trip_count,
    SUM(sales_amt) AS revenue
FROM transportation.gold.fact_trips
WHERE national_holiday = true
GROUP BY holiday_name;
```

---

## 🔮 Future Enhancements

| Enhancement | Description | Priority |
|-------------|-------------|----------|
| **Regional Holidays** | Add state-specific and festival holidays | High |
| **SCD Type 2** | Full historical tracking for city dimension | High |
| **Data Quality Rules** | DLT Expectations for validation | Medium |
| **Unity Catalog Lineage** | End-to-end data lineage tracking | Medium |
| **Anomaly Detection** | ML-based fraud detection for trips | Low |
| **Real-time Dashboard** | Streaming dashboards with Genie | Low |
| **Incremental Upserts** | `apply_changes()` for merge operations | Medium |

---

## 📂 Output Summary

### Tables Created

| Layer | Object | Type |
|-------|--------|------|
| Bronze | `transportation.bronze.city` | Materialized View |
| Bronze | `transportation.bronze.trips` | Streaming Table |
| Silver | `transportation.silver.calendar` | Materialized View |
| Silver | `transportation.silver.city` | Materialized View |
| Silver | `transportation.silver.trips` | Streaming Table |
| Gold | `transportation.gold.fact_trips` | View |
| Gold | `transportation.gold.fact_trips_<city>` | Secure Views (10) |

### Pipeline Statistics

| Metric | Value |
|--------|-------|
| Total Records Processed | 500,000+ |
| Pipeline Run Time | ~27 minutes |
| Tables Created | 17 |
| Performance Events | 34 |
| Upserted Records | 3,700+ |

---

