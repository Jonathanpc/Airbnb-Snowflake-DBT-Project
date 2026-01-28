# AWS DBT Snowflake Data Pipeline Project

A comprehensive data transformation pipeline built with dbt (Data Build Tool) and Snowflake, demonstrating modern data warehousing practices with incremental loading, Jinja templating, and multi-layer data architecture.

## 📋 Table of Contents

- [Project Overview](#overview)
- [Architecture](#architecture)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [Key Learning Outcomes](#key-learning-outcomes)
- [Technical Challenges & Solutions](#technical-challenges--solutions)
- [Setup Instructions](#setup-instructions)
- [Running the Project](#running-the-project)
- [Best Practices Learned](#best-practices-learned)

## 🎯 Overview

This project implements a three-tier data transformation pipeline (Bronze → Silver → Gold) for Airbnb booking data using dbt and Snowflake. The pipeline demonstrates:

- **Incremental loading** strategies for efficient data processing
- **Data quality transformations** from raw to analytics-ready datasets
- **Dimensional modeling** with fact and dimension tables
- **Slowly Changing Dimensions (SCD)** using dbt snapshots
- **Jinja templating** for dynamic SQL generation

## 🏗️ Architecture

### Tech Stack

<img width="686" height="365" alt="Screenshot 2026-01-28 at 6 04 05 PM" src="https://github.com/user-attachments/assets/2b4481f2-9f33-4eff-addf-d0bba5b17c30" />


**Core Technologies:**
- **AWS S3**: Raw data storage (Data Lake)
- **Snowflake**: Cloud data warehouse for processing and analytics
- **dbt (Data Build Tool)**: SQL-based transformation framework
- **Python**: Runtime environment and dependency management
- **Jinja**: Dynamic SQL templating engine

### Data Pipeline Architecture

The project follows a **medallion architecture pattern**

```
┌─────────────┐
│   STAGING   │ ← Raw data sources (S3/CSV files)
└──────┬──────┘
       │
       ↓
┌─────────────┐
│   BRONZE    │ ← Incremental loading from staging
└──────┬──────┘   (Raw data preservation)
       │
       ↓
┌─────────────┐
│   SILVER    │ ← Data cleaning & transformations
└──────┬──────┘   (Business logic application)
       │
       ↓
┌─────────────┐
│    GOLD     │ ← Analytics-ready tables
└─────────────┘   (OBT, Facts, Dimensions)
```

### Data Flow

1. **Staging → Bronze**: Raw data ingestion with incremental loading based on `CREATED_AT` timestamps
2. **Bronze → Silver**: Data cleaning, type casting, and business logic application
3. **Silver → Gold**: Dimensional modeling, fact table creation, and snapshot management

## 🛠️ Technologies Used

- **dbt Core** (v1.11.2): Data transformation and modeling
- **Snowflake**: Cloud data warehouse
- **Python** (3.12): Runtime environment
- **Jinja**: Dynamic SQL templating
- **SQL**: Core transformation logic

## 📁 Project Structure

```
aws_dbt_snowflake_project/
│
├── models/
│   └── sources/
│       ├── bronze/           # Incremental models from staging
│       │   ├── bronze_bookings.sql
│       │   ├── bronze_hosts.sql
│       │   └── bronze_listings.sql
│       │
│       ├── silver/           # Cleaned and transformed data
│       │   ├── silver_bookings.sql
│       │   ├── silver_hosts.sql
│       │   └── silver_listings.sql
│       │
│       ├── gold/            # Analytics layer
│       │   ├── obt.sql      # One Big Table
│       │   ├── fact.sql     # Fact table
│       │   └── ephemeral/   # Ephemeral dimension models
│       │       ├── bookings.sql
│       │       ├── hosts.sql
│       │       └── listings.sql
│       │
│       └── sources.yml      # Source definitions
│
├── snapshots/               # SCD Type 2 tracking
│   ├── dim_bookings.yml
│   ├── dim_hosts.yml
│   └── dim_listings.yml
│
├── macros/                  # Reusable SQL functions
│   ├── multiply.sql
│   ├── tag.sql
│   └── generate_schema_name.sql
│
├── analyses/                # Ad-hoc analysis queries
│   ├── explore.sql
│   ├── if_else.sql
│   └── loop.sql
│
├── docs/                    # Project documentation
│   ├── images/              # Architecture diagrams
│   └── Airbnb_Notes.drawio  # Source diagrams (Draw.io)
│
├── dbt_project.yml         # Project configuration
└── profiles.yml            # Connection credentials (NOT in Git)
```

## 📊 Project Documentation

### Architecture Diagrams

The project includes comprehensive architecture diagrams created with [Draw.io](https://www.drawio.com/):

**`docs/Airbnb_Notes.drawio`** - Complete visual documentation containing:

#### 🔧 Tech Stack Diagram
- **AWS S3** Data Lake for raw file storage
- **Snowflake** cloud data warehouse
- **dbt** transformation framework
- **IAM** security and access control
- Data flow between components

#### 📦 Data Layer Architecture
- **Staging**: Raw data ingestion point
- **Bronze**: Incremental loading from staging with `SELECT` statements
- **Silver**: Business logic transformations
- **Gold**: Analytics-ready models (OBT, FACT, Dimensions)

#### ⚙️ dbt Configuration Concepts
- **Adapters**: Connection layer to Snowflake
- **Models**: SQL SELECT statements as building blocks
- **Materializations**: View vs. Table vs. Incremental
- **Order of Precedence**: Inline config (most powerful) vs. `dbt_project.yml`

#### 📈 Incremental Loading Visuals
- Date range examples: Initial load (2015-01-01 to 2025-12-26)
- Incremental append: New data (2025-12-26 to 2025-12-27)
- Before/after states showing data growth

#### 🔄 UPSERT (Merge) Operations
- **V0 (Before)**: Shows duplicate records (ID 01 appears twice)
- **V1 (After)**: Shows updated records with latest values
- Visual demonstration of `unique_key` configuration

#### 🌟 Star Schema Design
- **FACT Table**: Central metrics table
- **Dimension Tables**:
  - `dim_hosts`: Host attributes
  - `dim_listings`: Property details
  - `dim_bookings`: Booking information
- Relationship arrows showing foreign keys

#### 🔍 One Big Table (OBT)
- Denormalized structure joining all entities
- Metadata-driven pipeline configuration approach
- Dynamic column selection using Jinja

#### 🧪 Testing Framework
- Source data validation
- Model testing strategies
- Data quality checkpoints

### Using the Diagrams

**To view/edit diagrams:**
1. Open `docs/Airbnb_Notes.drawio` in [diagrams.net](https://app.diagrams.net/)
2. Or install Draw.io desktop app: https://www.drawio.com/

**To export for README:**
1. Open diagram in Draw.io
2. File → Export as → PNG/SVG
3. Save to `docs/images/`
4. Reference in README with: `![Diagram](./docs/images/diagram_name.png)`

### Recommended Exports

For the README, consider exporting these sections separately:

1. **tech_stack.png** - Overview of S3, Snowflake, dbt integration
2. **data_flow.png** - Bronze → Silver → Gold pipeline
3. **incremental_loading.png** - Date-based incremental load example
4. **upsert_example.png** - Before/after UPSERT operation
5. **star_schema.png** - FACT and dimension table relationships
6. **concepts_overview.png** - All core concepts on one page

## 📚 Key Learning Outcomes

### 1. ETL Pipeline Architecture

**Concepts Learned:**
- **Extract**: Loading raw data from S3 Data Lake into Snowflake staging area
- **Transform**: Applying business logic and data quality rules
- **Load**: Moving transformed data through Bronze → Silver → Gold layers

**Architecture Components:**
- **S3 Data Lake**: Raw CSV file storage
- **Snowflake**: Cloud data warehouse for processing
- **dbt**: SQL-based transformation orchestration
- **IAM Security**: Role-based access control for secure data operations

### 2. dbt Adapters & Connections

Learned how dbt uses adapters to connect to different data warehouses:

```yaml
# profiles.yml
aws_dbt_snowflake_project:
  target: dev
  outputs:
    dev:
      type: snowflake  # Adapter type
      account: YOUR_ACCOUNT
      # ... other connection details
```

**Key Insight**: Adapters translate dbt's generic SQL into warehouse-specific SQL dialects.

### 3. Materialization Strategies

Understanding when to use different materializations:

| Strategy | Use Case | Performance | Storage |
|----------|----------|-------------|---------|
| **View** | Small datasets, frequently changing logic | Fast build, slow query | No storage |
| **Table** | Large datasets, stable logic | Slow build, fast query | High storage |
| **Incremental** | Large datasets with time-series data | Fast build, fast query | Moderate storage |
| **Ephemeral** | Intermediate CTEs, not exposed to end users | No build | No storage |

```sql
-- Configuration in model
{{ config(materialized='incremental') }}
```

**Order of Precedence** (Most to Least Powerful):
1. **Inline Config** (inside .sql file) - `{{ config(...) }}`
2. **dbt_project.yml** (project-level configuration)

### 4. Sources & Staging

Learned to reference source tables using `source()` function:

```sql
-- sources.yml
version: 2
sources:
  - name: staging
    database: AIRBNB
    schema: STAGING
    tables:
      - name: bookings
      - name: hosts
      - name: listings
```

```sql
-- In model: bronze_bookings.sql
SELECT * FROM {{ source('staging', 'bookings') }}
```

**Benefits:**
- Enables dbt to track data lineage
- Allows for source freshness testing
- Centralizes source definitions

### 5. Incremental Loading with Jinja

Implemented efficient incremental loading strategies:

**Example Scenario:**
- **Initial Load**: 2015-01-01 TO 2025-12-26 (full historical data)
- **Incremental Load**: 2025-12-26 TO 2025-12-27 (only new data)

```sql
{{ config(materialized='incremental') }}

SELECT * FROM {{ source('staging', 'bookings') }}

{% if is_incremental() %}
    WHERE CREATED_AT > (SELECT COALESCE(MAX(CREATED_AT), '1900-01-01') FROM {{ this }})
{% endif %}
```

**Key Insight**: The `{{ this }}` keyword references the current model's table in the warehouse, enabling incremental updates.

**Benefits:**
- Reduces processing time by only loading new/changed data
- Lowers warehouse compute costs
- Enables near-real-time data pipelines

### 6. Jinja Templating for Metadata-Driven Pipelines

Developed dynamic SQL generation using Jinja for complex joins and column selections:

```sql
-- Metadata-driven approach using configs
{% set configs = [
    {"table": "AIRBNB.SILVER.SILVER_BOOKINGS", "columns": "SILVER_bookings.*", "alias": "bookings"},
    {"table": "AIRBNB.SILVER.SILVER_LISTINGS", "columns": "SILVER_listings.*", "alias": "listings"},
    {"table": "AIRBNB.SILVER.SILVER_HOSTS", "columns": "SILVER_hosts.*", "alias": "hosts"}
] %}

SELECT
    {% for config in configs %}
        {{ config['columns'] }} {% if not loop.last %}, {% endif %}
    {% endfor %}
FROM {{ configs[0]['table'] }} AS {{ configs[0]['alias'] }}
{% for config in configs[1:] %}
    LEFT JOIN {{ config['table'] }} AS {{ config['alias'] }}
        ON bookings.listing_id = {{ config['alias'] }}.listing_id
{% endfor %}
```

**Benefits:**
- Single source of truth for table relationships
- Easy to add/remove tables without rewriting entire queries
- Reduces code duplication

### 7. Custom Macros for Reusability

Created reusable SQL functions to maintain DRY (Don't Repeat Yourself) principles:

```sql
-- macros/multiply.sql
{% macro multiply(x, y, precision) %}
    round({{x}} * {{y}}, {{precision}})
{% endmacro %}

-- Usage in model
SELECT 
    booking_id,
    {{ multiply('price', 'quantity', 2) }} AS total_amount
FROM bookings
```

```sql
-- macros/tag.sql
{% macro tag(col) %}
    CASE
        WHEN {{col}} < 100 THEN 'Low'
        WHEN {{col}} < 200 THEN 'Medium'
        ELSE 'High'
    END
{% endmacro %}

-- Usage
SELECT 
    listing_id,
    price,
    {{ tag('price') }} AS price_category
FROM listings
```

### 8. UPSERT Logic (Merge Operations)

Learned how incremental models handle updates and inserts:

**Bronze Layer (Before UPSERT):**
```
ID  Name
01  abc
02  xyz
03  edf
01  aaa  ← Duplicate ID with updated name
```

**Silver Layer (After UPSERT - V1):**
```
ID  Name
01  aaa  ← Updated record
02  xyz
03  edf
```

**Configuration:**
```sql
{{ config(
    materialized='incremental',
    unique_key='ID'  -- dbt will update records with matching IDs
) }}
```

### 9. Dimensional Modeling (Star Schema)

Implemented a star schema in the Gold layer:

**Star Schema Components:**
- **FACT Table**: Contains measurable metrics (bookings, revenue, quantities)
- **Dimension Tables**: Contains descriptive attributes
  - `dim_hosts`: Host information (name, response_rate, etc.)
  - `dim_listings`: Property details (price, bedrooms, location)
  - `dim_bookings`: Booking details (dates, guest info)

```sql
-- Fact table structure
SELECT
    booking_id,           -- Primary key
    listing_id,           -- Foreign key to dim_listings
    host_id,              -- Foreign key to dim_hosts
    booking_date,
    total_amount,         -- Metric
    night_count          -- Metric
FROM ...
```

**Benefits:**
- Optimized for analytical queries
- Reduces data redundancy
- Improves query performance

### 10. One Big Table (OBT)

Created denormalized OBT for simplified analytics:

```sql
-- gold/obt.sql
-- Joins all dimensions into a single wide table
SELECT
    bookings.*,
    listings.property_type,
    listings.price AS listing_price,
    hosts.host_name,
    hosts.response_rate
FROM silver_bookings bookings
LEFT JOIN silver_listings listings ON bookings.listing_id = listings.listing_id
LEFT JOIN silver_hosts hosts ON listings.host_id = hosts.host_id
```

**Use Case**: When analysts need quick access to all data without writing complex joins.

### 11. Schema Management & Custom Naming

Implemented custom schema naming convention to avoid default `dbt_` prefixes:

```sql
-- macros/generate_schema_name.sql
{% macro generate_schema_name(custom_schema_name, node) -%}
    {%- if custom_schema_name is none -%}
        {{ target.schema }}
    {%- else -%}
        {{ custom_schema_name | trim }}
    {%- endif -%}
{%- endmacro %}
```

**Result**: Tables created directly in specified schemas (BRONZE, SILVER, GOLD) instead of `dbt_bronze`, `dbt_silver`, etc.

### 12. Testing & Data Quality

Learned to implement data quality tests:

```yaml
# In sources.yml or schema.yml
version: 2
models:
  - name: silver_bookings
    columns:
      - name: booking_id
        tests:
          - unique
          - not_null
      - name: total_amount
        tests:
          - not_null
          - dbt_utils.accepted_range:
              min_value: 0
```

**Test Types:**
- **Source tests**: Validate raw data quality
- **Model tests**: Ensure transformations maintain data integrity
- **Custom tests**: Business-specific validation rules

## 🚧 Technical Challenges & Solutions

### Challenge 1: `{{ this }}` Reference in Compilation

**Problem**: Using `{{ this }}` in incremental models failed during `dbt compile` because the table doesn't exist yet at compile time.

**Solution**: 
- During development and initial testing, use `{{ ref('bronze_bookings') }}` for validation
- Switch back to `{{ this }}` when ready for `dbt run`
- Understand that `{{ this }}` is resolved at runtime, not compile time

```sql
-- Development approach
WHERE CREATED_AT > (SELECT MAX(CREATED_AT) FROM {{ ref('bronze_bookings') }})

-- Production approach
WHERE CREATED_AT > (SELECT MAX(CREATED_AT) FROM {{ this }})
```

### Challenge 2: Schema Conflicts During Incremental Runs

**Problem**: Running `dbt run` on the entire project caused data to continuously append to already-filled BRONZE schema tables, leading to failures in downstream SILVER transformations.

**Solution**:
1. **Clear schemas** before each full run to ensure clean state
2. **Use selective runs** instead of running entire project:
   ```bash
   # Instead of: dbt run
   # Use targeted runs:
   dbt run --select bronze_bookings
   dbt run --select silver_bookings
   dbt run --select gold.obt
   ```
3. Implemented proper **unique key constraints** in incremental models:
   ```sql
   {{ config(materialized='incremental', unique_key='BOOKING_ID') }}
   ```

**Lesson Learned**: Incremental models require careful orchestration. Always use `--select` for targeted runs during development to avoid unintended data duplication.

### Challenge 3: Credential Management 😅

**Problem**: Initially committed `profiles.yml` containing sensitive Snowflake credentials to Git.

**Solution**:
- Removed `profiles.yml` from version control
- Added to `.gitignore`
- Moved credentials to `~/.dbt/profiles.yml` (outside project directory)
- Changed compromised passwords immediately

**Security Best Practice**: Never commit credential files to version control.

## 🚀 Setup Instructions

### Prerequisites

- Python 3.12+
- Snowflake account
- Git

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd aws_dbt_snowflake_project
   ```

2. **Create virtual environment**
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install dbt-core dbt-snowflake
   ```

4. **Configure dbt profile**
   
   Create `~/.dbt/profiles.yml`:
   ```yaml
   aws_dbt_snowflake_project:
     target: dev
     outputs:
       dev:
         type: snowflake
         account: YOUR_ACCOUNT
         user: YOUR_USERNAME
         password: YOUR_PASSWORD
         role: YOUR_ROLE
         database: AIRBNB
         warehouse: COMPUTE_WH
         schema: dbt_schema
         threads: 1
   ```

5. **Test connection**
   ```bash
   cd aws_dbt_snowflake_project
   dbt debug
   ```

## 🏃 Running the Project

### Initial Setup

1. **Run seeds** (if applicable):
   ```bash
   dbt seed
   ```

2. **Run models in order**:
   ```bash
   # Bronze layer
   dbt run --select bronze_bookings bronze_hosts bronze_listings
   
   # Silver layer
   dbt run --select silver_bookings silver_hosts silver_listings
   
   # Gold layer
   dbt run --select obt fact
   ```

3. **Create snapshots**:
   ```bash
   dbt snapshot
   ```

### Development Workflow

```bash
# Compile models to check generated SQL
dbt compile --select model_name

# Run specific model
dbt run --select model_name

# Run model and downstream dependencies
dbt run --select model_name+

# Test data quality
dbt test

# Generate documentation
dbt docs generate
dbt docs serve
```

### Useful Commands

```bash
# Full refresh (drop and recreate tables)
dbt run --full-refresh

# Run models with specific tag
dbt run --select tag:bronze

# Run specific schema
dbt run --select silver.*
```

## ✅ Best Practices Learned

1. **Use `ref()` and `source()` functions**: Enables dbt to build dependency graph and run models in correct order

2. **Implement incremental loading**: Reduces processing time and warehouse costs for large datasets

3. **Separate concerns by layer**: 
   - Bronze: Raw data preservation
   - Silver: Business logic and cleaning
   - Gold: Analytics-optimized structures

4. **Use macros for repeated logic**: Maintains consistency and reduces code duplication

5. **Version control hygiene**: Never commit credentials, use `.gitignore` properly

6. **Targeted runs during development**: Use `--select` to run specific models and avoid cascading failures

7. **Schema naming conventions**: Customize schema generation to match organizational standards

8. **Documentation**: Use dbt's built-in documentation features for data catalog

## 🔍 Key Concepts Demonstrated

- **Materialization strategies**: Table, incremental, ephemeral
- **Jinja templating**: Dynamic SQL generation and control flow
- **Macros**: Reusable SQL components
- **Sources**: External data references
- **Snapshots**: SCD Type 2 implementation
- **Tests**: Data quality validation
- **Analyses**: Ad-hoc exploration queries

## 📊 Data Models

### Bronze Layer
- `bronze_bookings`: Incremental booking data
- `bronze_hosts`: Incremental host information
- `bronze_listings`: Incremental listing details

### Silver Layer
- `silver_bookings`: Cleaned bookings with calculated total amounts
- `silver_hosts`: Host data with response rate categorization
- `silver_listings`: Listings with price categorization tags

### Gold Layer
- `obt`: One Big Table joining all entities
- `fact`: Fact table with key metrics
- `dim_bookings`, `dim_hosts`, `dim_listings`: SCD Type 2 dimensions

## 🎓 Learning Journey

This project represents a hands-on exploration of modern data engineering practices. Through building this pipeline, I gained practical experience in:

### Core Concepts Mastered

#### 1. **ETL Architecture** 
Understanding the complete data flow from raw files in S3 through Snowflake staging to final analytics tables.

#### 2. **dbt Fundamentals**
- **Adapters**: How dbt connects to different warehouses
- **Materialization**: When to use views, tables, incremental models, and ephemeral CTEs
- **Configuration Precedence**: Understanding inline config vs. `dbt_project.yml`

#### 3. **Data Modeling Approaches**
- **Medallion Architecture**: Bronze (raw) → Silver (cleaned) → Gold (analytics-ready)
- **Star Schema**: Fact and dimension tables for analytical workloads
- **OBT (One Big Table)**: Denormalized wide tables for simplified querying

#### 4. **Advanced dbt Techniques**
- **Incremental Loading**: Time-based incremental strategies for efficiency
- **Macros**: Reusable SQL functions for DRY code
- **Jinja Templating**: Dynamic SQL generation and control flow
- **Metadata-Driven Pipelines**: Configuration-based table joins
- **UPSERT Logic**: Handling updates and inserts with unique keys

#### 5. **Data Quality & Testing**
- **Source Testing**: Validating raw data
- **Model Testing**: Ensuring transformation integrity
- **Schema Tests**: Enforcing data contracts

### Real-World Problem Solving

The challenges encountered and solved (particularly around incremental loading, schema management, and the `{{ this }}` compilation issue) provided valuable insights into production data pipeline development:

**Key Realizations:**
1. **Compile vs. Runtime**: `{{ this }}` works at runtime, not compile time
2. **Incremental Strategy**: Requires careful orchestration to avoid data duplication
3. **Security First**: Never commit credentials to version control
4. **Targeted Execution**: Use `--select` for development to avoid cascading failures
5. **Configuration is King**: Proper materialization and unique keys are critical

### Documentation & Visualization

All concepts, architectures, and data flows are documented in **`docs/Airbnb_Notes.drawio`**, including:
- Complete tech stack diagram
- Data flow visualizations  
- Incremental loading examples with date ranges
- UPSERT operation illustrations
- Star schema design
- Metadata-driven pipeline configurations

This visual documentation serves as both a learning artifact and reference guide for the project.

## 📝 License

This project is for educational and resume purposes.

**Author:** Jonathan Perez-Castro  
**Institution:** Rutgers University New Brunswick
**Contact:** yeriel1322@gmail.com
**LinkedIn:** [linkedin.com/in/jonathanpc](https://linkedin.com/in//jonathan-pc15)


**Note:** Remember to replace all placeholder values (`<<...>>`) with actual configuration values before running the pipeline.

---

**Note**: This project is part of my data engineering learning journey. The approaches and solutions documented here reflect my understanding and problem-solving process as I learned dbt and Snowflake integration.

