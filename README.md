# End-to-End Data Engineering Pipeline with PySpark & DBT on Databricks

A production-grade data engineering pipeline implementing the Medallion Architecture on Databricks using PySpark, Delta Lake, and DBT.

## Overview

This project demonstrates modern data engineering best practices including scalable ingestion with PySpark Structured Streaming, modular transformation logic, and advanced dimensional modeling with Slowly Changing Dimensions Type 2.

## Architecture

The pipeline follows a 3-layer Medallion Architecture:

- **Bronze Layer**: Raw CSV data ingestion using PySpark Structured Streaming with checkpoint volumes
- **Silver Layer**: Data cleaning, validation, and schema harmonization
- **Gold Layer**: Analytics-ready dimensional tables with SCD Type 2 and historical tracking via DBT

## Technologies

- Databricks
- PySpark (Structured Streaming & Functions)
- Delta Lake
- DBT (Data modeling & transformations)
- Databricks SQL
- Python

## Quick Start

### 1. Clone Repository
```bash
git clone https://github.com/dharmik212/End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT.git
```

### 2. Setup DBT
```bash
pip install dbt-databricks
```

### 3. Configure Credentials
Update `~/.dbt/profiles.yml` with your Databricks connection details:
```yaml
databricks:
  target: dev
  outputs:
    dev:
      type: databricks
      host: <your-databricks-host>
      http_path: <your-cluster-http-path>
      token: <your-token>
      schema: <your-schema>
```

### 4. Run Pipeline
```bash
# Execute PySpark notebooks
%run ./notebooks/01_bronze_ingestion.py
%run ./notebooks/02_silver_transformation.py

# Execute DBT models
dbt run
dbt test
```

## Key Features

- **Dynamic Data Ingestion**: Function-based PySpark for reusable, scalable processing
- **Incremental Processing**: Checkpoint volumes ensure exactly-once delivery
- **Advanced DBT**: Incremental models, snapshots, SCD Type 2, and automated testing
- **Data Quality**: Built-in validation checks across all layers
- **Production-Ready**: Error handling, monitoring, and serverless compute

## Project Structure

```
├── notebooks/
│   ├── 01_bronze_ingestion.py
│   ├── 02_silver_transformation.py
│   └── utilities/
├── dbt/
│   ├── models/
│   │   ├── bronze/
│   │   ├── silver/
│   │   └── gold/
│   └── dbt_project.yml
├── data/
│   └── sources/
└── README.md
```

## Data Quality & Testing

All layers include validation with DBT tests:

```bash
dbt test
```

Tests validate:
- Uniqueness constraints on dimension keys
- Null value checks on critical columns
- Referential integrity between tables
- Custom business logic

## Performance Optimization

- **Incremental Models**: Only process new/modified records
- **Partitioning**: Tables partitioned by date and business keys
- **Caching**: Silver tables materialized for frequent queries

## Getting Started

1. **Upload sample data** to Databricks File System
2. **Run Bronze notebook** to ingest raw data
3. **Run Silver notebook** to transform and clean
4. **Execute DBT** to model Gold layer analytics
5. **Explore results** via Databricks SQL

## Support

- Repository: https://github.com/dharmik212/End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT

---

**Maintainer**: Dharmik Kurlawala  
**Last Updated**: November 2025
