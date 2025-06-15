# SQL Data Warehouse & Analytics Project

This comprehensive project demonstrates an end-to-end data engineering and analytics solution, showcasing modern data warehouse architecture, advanced ETL processes, and sophisticated SQL-based analytics. Built with SQL Server, it implements the Medallion Architecture pattern and provides extensive analytical capabilities for data-driven decision making.

---

## Project Overview

This project implements a complete data warehousing and analytics solution that consolidates data from multiple source systems (CRM and ERP) into a unified, analytics-ready data warehouse. The solution follows industry best practices and demonstrates proficiency in:

- **Data Architecture Design** using Medallion Architecture
- **ETL Pipeline Development** with SQL Server
- **Data Quality Management** and validation
- **Dimensional Modeling** with Star Schema
- **Advanced SQL Analytics** and Business Intelligence
- **Comprehensive Data Reporting** and insights

---

## Architecture Overview

The project implements the **Medallion Architecture** with three distinct layers:

![High Level Architecture](docs/data_architecture.png)

### Architecture Layers:

1. **Bronze Layer (Raw Data)**
   - Stores data exactly as received from source systems
   - Minimal processing and validation
   - Serves as the single source of truth

2. **Silver Layer (Cleaned Data)**
   - Advanced data cleansing and standardization
   - Complex business rule application
   - Data type conversions and validations
   - Derived column calculations
   - Data enrichment and normalization

3. **Gold Layer (Business Ready)**
   - Dimensional modeling (Star Schema)
   - Aggregated and enriched data
   - Optimized for analytics and reporting

---

## Data Flow & Integration

![Data Flow](docs/data_flow.png)

The data integration follows a systematic approach:

![Data Integration Model](docs/data_integration.png)

**Source Systems:**
- **CRM System**: Customer information, product details, sales transactions
- **ERP System**: Customer demographics, location data, product categories

---

## ETL Implementation

![ETL Methods](docs/ETL.png)

The project implements comprehensive ETL techniques:
- **Extraction**: Bulk insert from CSV files with error handling
- **Transformation**: Advanced data cleansing, standardization, and enrichment
- **Loading**: Incremental and full load strategies with performance monitoring
- **Data Quality**: Comprehensive validation and error handling

---

## Analytics Capabilities

![Analytics Roadmap](docs/Analytics_%20Roadmap.png)

The project includes a comprehensive analytics framework covering:

### **Exploratory Data Analysis (EDA)**
- **Database Exploration**: Schema and table structure analysis
- **Dimensions Exploration**: Unique values and data distribution
- **Date Range Exploration**: Temporal data boundaries and patterns
- **Measures Exploration**: Key business metrics and KPIs

### **Advanced Analytics Types**

#### **1. Magnitude Analysis**
- Customer distribution by geography and demographics
- Product distribution by categories
- Revenue analysis by various dimensions

#### **2. Ranking Analysis**
- Top/bottom performing products
- Customer ranking by revenue
- Performance comparisons using window functions

#### **3. Change Over Time Analysis**
- Monthly and yearly sales trends
- Growth pattern identification
- Seasonal analysis using date functions

#### **4. Cumulative Analysis**
- Running totals and moving averages
- Growth trajectory analysis
- Performance accumulation over time

#### **5. Performance Analysis**
- Year-over-year comparisons
- Month-over-month growth
- Trend analysis with LAG functions

#### **6. Data Segmentation**
- Customer segmentation (VIP, Regular, New)
- Product categorization by price ranges
- Behavioral pattern analysis

#### **7. Part-to-Whole Analysis**
- Market share calculations
- Contribution analysis by categories
- Percentage distributions

---

## Project Structure

```
SQL_DataWarehouse/
│
├── datasets/                           # Source data files
│   ├── source_crm/                     # CRM system data
│   │   ├── cust_info.csv
│   │   ├── prd_info.csv
│   │   └── sales_details.csv
│   └── source_erp/                     # ERP system data
│       ├── CUST_AZ12.csv
│       ├── LOC_A101.csv
│       └── PX_CAT_G1V2.csv
│
├── scripts/                            # SQL implementation scripts
│   ├── init_database.sql               # Database and schema creation
│   ├── data_warehouse/                 # Data warehouse ETL scripts
│   │   ├── bronze/                     # Bronze layer implementation
│   │   │   ├── ddl_bronze.sql
│   │   │   └── proc_load_bronze.sql
│   │   ├── silver/                     # Silver layer implementation
│   │   │   ├── ddl_silver.sql
│   │   │   └── proc_load_silver.sql
│   │   └── gold/                       # Gold layer implementation
│   │       └── ddl_gold.sql
│   └── data_analytics/                 # Analytics and reporting scripts
│       ├── 01_database_exploration.sql
│       ├── 02_dimensions_exploration.sql
│       ├── 03_date_range_exploration.sql
│       ├── 04_measures_exploration.sql
│       ├── 05_magnitude_analysis.sql
│       ├── 06_ranking_analysis.sql
│       ├── 07_change_over_time_analysis.sql
│       ├── 08_cumulative_analysis.sql
│       ├── 09_performance_analysis.sql
│       ├── 10_data_segmentation.sql
│       ├── 11_part_to_whole_analysis.sql
│       ├── 12_report_customers.sql
│       └── 13_report_products.sql
│
├── tests/                              # Data quality and validation
│   ├── quality_checks_silver.sql
│   └── quality_checks_gold.sql
│
├── docs/                               # Project documentation
│   ├── data_catalog.md                 # Data dictionary and catalog
│   ├── naming_conventions.md           # Coding standards
│   ├── *.png                          # Architecture diagrams
│   └── drawio/                        # Source diagram files
│
├── .gitignore                          # Git ignore rules
└── README.md                           # Project documentation
```

---

## Advanced Data Transformations (Silver Layer)

The Silver layer implements sophisticated data transformation techniques:

### Data Cleansing & Standardization

#### **Customer Data Normalization**
```sql
-- Gender Standardization with Hidden Character Removal
CASE 
    WHEN UPPER(TRIM(
        REPLACE(REPLACE(REPLACE(gen, CHAR(13), ''), CHAR(10), ''), CHAR(160), '')
    )) IN ('F', 'FEMALE') THEN 'Female'
    WHEN UPPER(TRIM(
        REPLACE(REPLACE(REPLACE(gen, CHAR(13), ''), CHAR(10), ''), CHAR(160), '')
    )) IN ('M', 'MALE') THEN 'Male'
    ELSE 'n/a'
END AS gender

-- Country Code Normalization
CASE
    WHEN TRIM(cntry) = 'DE' THEN 'Germany'
    WHEN TRIM(cntry) IN ('US', 'USA') THEN 'United States'
    WHEN TRIM(cntry) = '' OR cntry IS NULL THEN 'n/a'
    ELSE TRIM(cntry)
END AS country
```

### Advanced Business Logic

#### **Sales Calculation Validation & Correction**
```sql
-- Recalculate Sales Amount if Invalid
CASE 
    WHEN sls_sales IS NULL OR sls_sales <= 0 
         OR sls_sales != sls_quantity * ABS(sls_price) 
        THEN sls_quantity * ABS(sls_price)
    ELSE sls_sales
END AS sales_amount

-- Product Lifecycle Management using Window Functions
CAST(
    LEAD(prd_start_dt) OVER (PARTITION BY prd_key ORDER BY prd_start_dt) - 1 
    AS DATE
) AS product_end_date
```

---

## Dimensional Modeling & Star Schema

![Data Model](docs/data_model.png)

The Gold layer implements a sophisticated **Star Schema** optimized for analytical queries:

### Design Principles & Architecture

- **Surrogate Keys**: Auto-generated integer keys for all dimensions
- **Slowly Changing Dimensions**: Type 1 SCD implementation
- **Fact Table Optimization**: Foreign keys to dimensions with additive measures
- **Denormalization**: Flattened dimensions for query performance

### Multi-Source Data Integration

```sql
-- Customer Dimension: Merging CRM + ERP Data
SELECT
    ci.cst_id AS customer_id,
    ci.cst_firstname AS first_name,
    la.cntry AS country,  -- From ERP Location
    CASE 
        WHEN ci.cst_gndr != 'n/a' THEN ci.cst_gndr  -- CRM Priority
        ELSE COALESCE(ca.gen, 'n/a')                -- ERP Fallback
    END AS gender,
    ca.bdate AS birthdate  -- From ERP Demographics
FROM silver.crm_cust_info ci
LEFT JOIN silver.erp_cust_az12 ca ON ci.cst_key = ca.cid
LEFT JOIN silver.erp_loc_a101 la ON ci.cst_key = la.cid
```

---

## Advanced Analytics Examples

### **Customer Segmentation Analysis**
```sql
-- Segment customers into VIP, Regular, and New categories
WITH customer_spending AS (
    SELECT
        c.customer_key,
        SUM(f.sales_amount) AS total_spending,
        DATEDIFF(month, MIN(order_date), MAX(order_date)) AS lifespan
    FROM gold.fact_sales f
    LEFT JOIN gold.dim_customers c ON f.customer_key = c.customer_key
    GROUP BY c.customer_key
)
SELECT 
    CASE 
        WHEN lifespan >= 12 AND total_spending > 5000 THEN 'VIP'
        WHEN lifespan >= 12 AND total_spending <= 5000 THEN 'Regular'
        ELSE 'New'
    END AS customer_segment,
    COUNT(customer_key) AS total_customers
FROM customer_spending
GROUP BY customer_segment;
```

### **Year-over-Year Performance Analysis**
```sql
-- Compare product performance year-over-year
SELECT
    product_name,
    order_year,
    current_sales,
    LAG(current_sales) OVER (PARTITION BY product_name ORDER BY order_year) AS py_sales,
    current_sales - LAG(current_sales) OVER (PARTITION BY product_name ORDER BY order_year) AS yoy_change
FROM yearly_product_sales
ORDER BY product_name, order_year;
```

---

## Data Catalog

### Gold Layer Schema

#### `gold.dim_customers`
| Column | Type | Description |
|--------|------|-------------|
| customer_key | INT | Surrogate key (Primary Key) |
| customer_id | INT | Business key from CRM |
| customer_number | NVARCHAR(50) | Customer reference number |
| first_name | NVARCHAR(50) | Customer first name |
| last_name | NVARCHAR(50) | Customer last name |
| country | NVARCHAR(50) | Customer country |
| marital_status | NVARCHAR(50) | Marital status (standardized) |
| gender | NVARCHAR(50) | Gender (standardized) |
| birthdate | DATE | Date of birth |
| create_date | DATE | Customer creation date |

#### `gold.dim_products`
| Column | Type | Description |
|--------|------|-------------|
| product_key | INT | Surrogate key (Primary Key) |
| product_id | INT | Business key from CRM |
| product_number | NVARCHAR(50) | Product reference number |
| product_name | NVARCHAR(50) | Product name |
| category_id | NVARCHAR(50) | Product category ID |
| category | NVARCHAR(50) | Product category |
| subcategory | NVARCHAR(50) | Product subcategory |
| maintenance | NVARCHAR(50) | Maintenance requirement |
| cost | INT | Product cost |
| product_line | NVARCHAR(50) | Product line |
| start_date | DATE | Product availability start date |

#### `gold.fact_sales`
| Column | Type | Description |
|--------|------|-------------|
| order_number | NVARCHAR(50) | Sales order number |
| product_key | INT | Foreign key to dim_products |
| customer_key | INT | Foreign key to dim_customers |
| order_date | DATE | Order placement date |
| shipping_date | DATE | Order shipping date |
| due_date | DATE | Payment due date |
| sales_amount | INT | Total sales amount |
| quantity | INT | Quantity ordered |
| price | INT | Unit price |

---

## Testing & Quality Assurance

### Comprehensive Data Quality Validation

#### Silver Layer Validation
- Primary key uniqueness and null checks
- Data standardization verification
- Date range and format validation
- Cross-field consistency checks

#### Gold Layer Validation
- Surrogate key uniqueness
- Referential integrity between fact and dimensions
- Data model relationship validation

---

## Technologies & Skills Demonstrated

### **Technologies Used**
- **Database**: Microsoft SQL Server
- **ETL**: T-SQL Stored Procedures
- **Data Modeling**: Star Schema Design
- **Analytics**: Advanced SQL Analytics
- **Version Control**: Git
- **Documentation**: Markdown
- **Diagramming**: Draw.io

### **Key Skills Showcased**
- **Data Architecture**: Medallion Architecture implementation
- **Advanced SQL Development**: Complex T-SQL programming with window functions
- **ETL Pipeline Development**: Extract, Transform, Load with error handling
- **Dimensional Modeling**: Star schema design and optimization
- **Data Quality Engineering**: Comprehensive validation and cleansing
- **Business Intelligence**: Advanced analytics and reporting
- **System Integration**: Multi-source data consolidation
- **Performance Optimization**: Query optimization and indexing strategies

---

## Analytics Script Library

The project includes 13 comprehensive analytics scripts covering:

1. **Database Exploration** - Schema and metadata analysis
2. **Dimensions Exploration** - Data distribution and unique values
3. **Date Range Exploration** - Temporal boundaries and patterns
4. **Measures Exploration** - Key business metrics and KPIs
5. **Magnitude Analysis** - Quantitative analysis by dimensions
6. **Ranking Analysis** - Top/bottom performers with window functions
7. **Change Over Time** - Trends and temporal analysis
8. **Cumulative Analysis** - Running totals and moving averages
9. **Performance Analysis** - Year-over-year and comparative analysis
10. **Data Segmentation** - Customer and product categorization
11. **Part-to-Whole Analysis** - Market share and contribution analysis
12. **Customer Reports** - Comprehensive customer analytics
13. **Product Reports** - Detailed product performance analysis

---

## Documentation

- **[Data Catalog](docs/data_catalog.md)** - Complete data dictionary
- **[Naming Conventions](docs/naming_conventions.md)** - Coding standards and guidelines
- **Architecture Diagrams** - Visual representations of the solution

---
## Reference

This project was developed following the comprehensive SQL bootcamp by **Data with Baraa**:

**[Data with Baraa SQL Bootcamp](https://youtube.com/playlist?list=PLNcg_FV9n7qZY_2eAtUzEUulNjTJREhQe&si=69f1kmj2RFzzDXBn)**

Special thanks to Baraa Khatib Salkini for providing excellent knowledge in modern data warehousing practices and advanced SQL analytics.

---

*Built with ❤️ and SQL - A Complete Data Warehouse & Analytics Solution*