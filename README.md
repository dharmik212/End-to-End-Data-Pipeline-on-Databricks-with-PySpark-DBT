# End-to-End Data Engineering Pipeline with PySpark & DBT on Databricks

A production-grade data engineering pipeline implementing the Medallion Architecture on Databricks. This project demonstrates modern data engineering practices including incremental processing, data quality automation, and advanced dimensional modeling.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Technologies](#technologies)
- [Project Structure](#project-structure)
- [Setup Instructions](#setup-instructions)
- [Pipeline Layers](#pipeline-layers)
- [Key Features](#key-features)
- [Getting Started](#getting-started)
- [Data Quality & Testing](#data-quality--testing)
- [Performance Optimization](#performance-optimization)
- [Monitoring & Logging](#monitoring--logging)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This pipeline processes CSV data sources through three distinct layers (Bronze, Silver, Gold) using the Medallion Architecture pattern. It demonstrates best practices in data engineering including:

- Scalable ingestion with PySpark Structured Streaming
- Modular, reusable transformation logic
- Advanced dimensional modeling with Slowly Changing Dimensions Type 2
- Automated data quality checks and governance
- Production-ready error handling and monitoring

## Architecture

The pipeline follows a **3-layer Medallion Architecture**:

```
CSV Source Data
      ↓
   BRONZE LAYER (Raw Ingestion)
      ↓ (PySpark Structured Streaming)
   SILVER LAYER (Cleaning & Transformation)
      ↓ (Data Validation & Schema Harmonization)
   GOLD LAYER (Analytics & Dimensional Modeling)
      ↓ (DBT Models & SCD Type 2)
   Ready for Analytics & BI
```

**Bronze Layer**: Immutable raw data storage with exactly-once delivery semantics

**Silver Layer**: Cleaned, validated, and harmonized data with business rules applied

**Gold Layer**: Analytics-ready dimensional tables with historical tracking and aggregations

## Technologies

| Component | Purpose |
|-----------|---------|
| **Databricks** | Unified analytics platform and compute engine |
| **PySpark** | Distributed data processing and streaming |
| **Delta Lake** | ACID-compliant data lake storage format |
| **DBT** | Data modeling, transformation, and testing framework |
| **Databricks SQL** | Interactive querying and analytics |
| **Python** | Dynamic programming and object-oriented utilities |
| **Git** | Version control and collaboration |

## Project Structure

```
End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT/
├── README.md                          # Project documentation
├── notebooks/
│   ├── 01_bronze_ingestion.py        # Raw data ingestion with PySpark Streaming
│   ├── 02_silver_transformation.py   # Data cleaning and quality checks
│   ├── 03_gold_modeling.py           # Analytics preparation (pre-DBT)
│   └── utilities/
│       ├── data_loader.py            # Reusable data loading functions
│       ├── validators.py             # Data quality validation logic
│       └── config.py                 # Configuration and constants
├── dbt/
│   ├── models/
│   │   ├── bronze/                   # Bronze layer models
│   │   ├── silver/                   # Silver layer models
│   │   └── gold/                     # Gold layer models (SCD Type 2, snapshots)
│   ├── tests/                        # Data quality and uniqueness tests
│   ├── macros/                       # Reusable DBT macros
│   ├── dbt_project.yml               # DBT project configuration
│   └── packages.yml                  # DBT package dependencies
├── data/
│   ├── sources/                      # Sample CSV source files
│   │   ├── customers.csv
│   │   ├── trips.csv
│   │   ├── drivers.csv
│   │   ├── locations.csv
│   │   ├── payments.csv
│   │   └── vehicles.csv
│   └── expected_outputs/             # Sample expected transformation outputs
├── docs/
│   ├── architecture_diagram.md       # Detailed architecture documentation
│   ├── dbt_lineage.md                # Data lineage documentation
│   └── transformation_logic.md       # Detailed transformation specifications
├── tests/
│   ├── integration_tests.py          # End-to-end pipeline tests
│   └── unit_tests.py                 # Component-level tests
├── images/
│   ├── architecture.png              # Architecture diagram
│   ├── bronze.png                    # Bronze layer visualization
│   ├── silver_cleaning_1.png         # Silver layer transformation example 1
│   ├── silver_cleaning_2.png         # Silver layer transformation example 2
│   ├── dbt_jinja.png                 # DBT configuration example
│   ├── dbt_SCD.png                   # SCD Type 2 implementation
│   ├── gold_analysis_1.png           # Gold layer analysis example 1
│   ├── gold_analysis_2.png           # Gold layer analysis example 2
│   └── gold_analysis_3.png           # Gold layer analysis example 3
├── config.yaml                       # Pipeline configuration
└── .gitignore                        # Git ignore rules

```

## Setup Instructions

### Prerequisites

- Databricks workspace (Free or paid tier)
- Python 3.9 or higher
- Git installed locally
- DBT CLI installed (`pip install dbt-databricks`)
- PySpark environment configured

### Step 1: Clone the Repository

```bash
git clone https://github.com/dharmik212/End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT.git
cd End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT
```

### Step 2: Set Up Databricks Environment

1. Create a Databricks cluster or use an existing one
2. Create a new notebook in your Databricks workspace
3. Clone this repository into Databricks:
   ```
   %sh git clone https://github.com/dharmik212/End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT.git /Workspace/Users/{your-email}/pipeline-project
   ```

### Step 3: Install Dependencies

In a Databricks notebook cell:

```python
%pip install dbt-databricks==1.7.0
%pip install databricks-cli
```

### Step 4: Configure DBT

1. Navigate to the `dbt/` directory
2. Update `dbt_project.yml` with your Databricks workspace details:
   ```yaml
   config-version: 2
   name: 'databricks_dbt_pipeline'
   version: '1.0.0'
   
   profile: 'databricks'
   
   model-paths: ["models"]
   analysis-paths: ["analysis"]
   test-paths: ["tests"]
   data-paths: ["data"]
   macro-paths: ["macros"]
   ```

3. Create `~/.dbt/profiles.yml` with your Databricks credentials:
   ```yaml
   databricks:
     target: dev
     outputs:
       dev:
         type: databricks
         host: <your-databricks-host>
         http_path: <your-cluster-http-path>
         token: <your-databricks-token>
         schema: <your-schema-name>
         threads: 4
         timeout_seconds: 300
   ```

### Step 5: Upload Sample Data

1. Upload CSV files from `data/sources/` to your Databricks File System:
   ```python
   dbutils.fs.put("/Volumes/your-catalog/your-schema/data/customers.csv", <file-content>, overwrite=True)
   ```

2. Or use the Databricks UI to upload files to `/Volumes/{catalog}/{schema}/data/`

## Pipeline Layers

### Bronze Layer: Raw Data Ingestion

**Purpose**: Capture raw source data exactly as received

**Implementation**:
- PySpark Structured Streaming ingests CSV files
- Checkpoint volumes ensure exactly-once delivery
- Data stored in Delta Lake format for ACID compliance
- No transformations applied; maintains data lineage

**Key Notebook**: `notebooks/01_bronze_ingestion.py`

**Example Logic**:
```python
from pyspark.sql.functions import current_timestamp

df = spark.readStream.option("cloudFiles.format", "csv").schema(schema).load("<source-path>")
df = df.withColumn("ingestion_timestamp", current_timestamp())
df.writeStream.option("checkpointLocation", "<checkpoint-path>").mode("append").toTable("bronze_customers")
```

---

### Silver Layer: Data Cleaning and Transformation

**Purpose**: Prepare clean, validated data for analytics

**Implementation**:
- Schema harmonization across data sources
- Null value handling and data type validation
- Duplicate detection and removal
- Business rule application
- Data quality checks and assertions

**Key Notebook**: `notebooks/02_silver_transformation.py`

**Example Transformations**:
- Standardize date formats
- Normalize column names
- Handle missing values with appropriate defaults
- Remove duplicates based on business keys
- Validate data against business rules

---

### Gold Layer: Analytics and Dimensional Modeling

**Purpose**: Deliver analytics-ready data with historical tracking

**Implementation**:
- DBT models for dimensional tables (customers, drivers, vehicles)
- Fact tables for transactions and events
- Slowly Changing Dimension Type 2 with effective dates
- Snapshots for historical change tracking
- Aggregated metrics and key performance indicators

**Key Features**:
- Incremental models for performance optimization
- SCD Type 2 implementation for dimension history
- Built-in data quality tests
- Automated lineage documentation

**Example DBT Model**:
```yaml
# models/gold/gold_customers_scd.sql
select
  customer_id,
  customer_name,
  email,
  phone,
  address,
  dbt_valid_from,
  dbt_valid_to,
  dbt_is_current
from {{ ref('silver_customers') }}
where dbt_valid_to is null  -- Active records only
```

## Key Features

### Dynamic Data Ingestion

Function-based PySpark implementation enables:
- Reusable code patterns across multiple entities
- Reduced code duplication and maintenance overhead
- Easy scaling to new data sources
- Consistent error handling across ingestions

### Incremental Processing

Checkpoint volumes provide:
- Exactly-once delivery semantics (no duplicates)
- Cost optimization by processing only new data
- Resumable pipelines from last checkpoint
- Fault tolerance and automatic recovery

### Modular Code Design

Python classes and utilities enable:
- Separation of concerns (extraction, transformation, loading)
- Unit testability of individual components
- Configuration management and parameterization
- Easy debugging and troubleshooting

### Advanced DBT Implementation

- **Incremental Models**: Only process changed data for performance
- **Snapshots**: Capture historical state changes automatically
- **SCD Type 2**: Track dimension attribute changes with effective dates
- **Tests**: Automated data quality validation (uniqueness, referential integrity, custom checks)
- **Lineage**: YAML/Jinja documentation of data dependencies
- **Macros**: Reusable transformation logic across models

### Interactive Analytics

- SQL queries for ad hoc exploration
- Dashboards and visualizations in Databricks
- Real-time metric calculation
- Stakeholder-ready reporting

### Production-Ready

- Serverless compute for cost optimization
- Comprehensive error handling and retry logic
- Structured logging for monitoring and debugging
- Performance optimization with caching and partitioning

## Getting Started

### 1. Run the Bronze Layer Ingestion

```python
%run ./notebooks/01_bronze_ingestion.py
```

This notebook will:
- Read CSV files from the data source directory
- Apply schema validation
- Write raw data to Bronze tables with streaming checkpoints

### 2. Run the Silver Layer Transformation

```python
%run ./notebooks/02_silver_transformation.py
```

This notebook will:
- Read from Bronze tables
- Apply cleaning and validation logic
- Write transformed data to Silver tables
- Generate data quality reports

### 3. Execute DBT Models

From your terminal or Databricks notebook:

```bash
dbt run --profiles-dir ~/.dbt --project-dir ./dbt
```

This will:
- Execute all Gold layer models
- Apply SCD Type 2 logic
- Create snapshots for historical tracking
- Run automated data quality tests

### 4. Run Tests and Validation

```bash
dbt test --profiles-dir ~/.dbt --project-dir ./dbt
```

This will:
- Validate uniqueness constraints
- Check for null values in critical columns
- Execute custom data quality tests
- Generate test reports

### 5. Generate Documentation

```bash
dbt docs generate --profiles-dir ~/.dbt --project-dir ./dbt
dbt docs serve
```

This will:
- Create interactive data lineage documentation
- Generate column-level metadata
- Serve documentation on localhost:8000

## Data Quality & Testing

### Built-in Data Quality Checks

All layers include validation:

**Bronze Layer**:
- Schema validation on ingestion
- Duplicate detection
- Data type conformance

**Silver Layer**:
- Null value checks
- Referential integrity validation
- Business rule enforcement
- Outlier detection

**Gold Layer**:
- Uniqueness constraints on dimension keys
- Not-null constraints on critical columns
- Custom business logic tests
- Referential integrity between fact and dimension tables

### Example DBT Test

```yaml
# dbt/tests/gold/test_customers_unique.sql
select customer_id, count(*) as cnt
from {{ ref('gold_customers') }}
where dbt_is_current = true
group by customer_id
having cnt > 1
```

## Performance Optimization

### Partitioning Strategy

Tables are partitioned by:
- **Date columns**: For time-series filtering
- **Business keys**: For parallel processing

### Incremental Model Strategy

Gold layer models use incremental updates:
- Only processes new or modified records
- Significantly reduces compute time
- Maintains full historical accuracy

### Caching and Materialization

- Silver tables materialized as Delta tables
- Gold tables materialized for frequent queries
- DBT ephemeral models for intermediate calculations

## Monitoring & Logging

### Pipeline Execution Logs

All notebooks include structured logging:
```python
import logging
logger = logging.getLogger(__name__)
logger.info(f"Processing {record_count} records from {source_name}")
```

### Data Quality Metrics

Track metrics at each layer:
- Record counts and changes
- Data completeness percentages
- Validation pass/fail rates
- Processing duration and performance

### DBT Artifacts

DBT generates execution reports:
- `target/manifest.json`: Complete lineage and metadata
- `target/run_results.json`: Execution results and timing
- `dbt_packages/`: Installed package versions

## Contributing

Contributions are welcome! Please follow these guidelines:

1. Create a feature branch: `git checkout -b feature/your-feature-name`
2. Make your changes and test thoroughly
3. Add or update documentation as needed
4. Commit with clear, descriptive messages: `git commit -m "Add feature: description"`
5. Push to your fork: `git push origin feature/your-feature-name`
6. Open a Pull Request with a detailed description

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support & Questions

For questions or issues:

1. Check existing GitHub Issues for solutions
2. Review the documentation in the `docs/` directory
3. Open a new GitHub Issue with a detailed description
4. Contact the maintainer at your-email@example.com

## Acknowledgments

- Databricks for the unified analytics platform
- DBT team for the modern data modeling framework
- PySpark community for distributed computing capabilities
- Open source data engineering community for best practices

---

**Last Updated**: November 2025  
**Maintainer**: Dharmik Kurlawala  
**Repository**: https://github.com/dharmik212/End-to-End-Data-Pipeline-on-Databricks-with-PySpark-DBT
