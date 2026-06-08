# Airline Analytics Data Warehouse

A comprehensive **Airline Analytics Data Warehouse** solution built using **Oracle Database** and **IBM InfoSphere DataStage**, designed to support enterprise-level analytics for Marketing, Finance, and Customer Care departments.

## 📌 Project Overview

This project implements a scalable dimensional data warehouse following the **Kimball Methodology**. It integrates operational airline data into a centralized analytical platform that enables:

- Frequent Flyer Analysis
- Revenue & Reservation Analytics
- Customer Support Performance Monitoring
- Promotional Campaign Evaluation
- Financial Yield Optimization

---

# 🏗 Architecture Overview

```text
+------------------+      +-------------------+      +-------------------+
|  Source Systems  | ---> | Staging (ODS)     | ---> | Core DWH (Star)   |
| CRM, Reservation |      | Change Data Capture|      | Conformed Schema  |
| Customer Care    |      |                   |      |                   |
+------------------+      +-------------------+      +-------------------+
                                                               |
                                                               v
                                                     +-------------------+
                                                     | Aggregated Marts  |
                                                     | Marketing/Finance |
                                                     +-------------------+
```

---

# 🎯 Business Processes

The warehouse supports three primary business processes.

## 1. Flight Activity

**Purpose:** Marketing & Loyalty Analysis

**Granularity:** One row per flight leg flown by a frequent flyer.

### Dimensions

- Dim_Frequent_Flyer
- Dim_Flight_Leg
- Dim_Fare_Basis
- Dim_Date
- Dim_Tier_Status

### Fact Table

**Fact_Flight_Activity**

Measures:

- Miles Earned
- Miles Redeemed
- Upgrade Count
- Base Fare Paid
- Overnight Stay Duration

---

## 2. Reservation Process

**Purpose:** Revenue & Financial Analysis

**Granularity:** One row per reservation transaction line item.

### Dimensions

- Dim_Frequent_Flyer
- Dim_Booking_Channel
- Dim_Promotion
- Dim_Date
- Dim_Geography

### Fact Table

**Fact_Reservation**

Measures:

- Gross Revenue
- Discount Amount
- Net Profit
- Ticket Taxes
- Commission Paid

---

## 3. Customer Care Interaction

**Purpose:** Customer Service & Satisfaction Analysis

**Granularity:** One row per support interaction.

### Dimensions

- Dim_Frequent_Flyer
- Dim_Interaction_Type
- Dim_Severity
- Dim_Date

### Fact Table

**Fact_Customer_Interaction**

Measures:

- Resolution Time
- CSAT Score
- Escalation Count

---

# ⭐ Data Warehouse Design

## Star Schema Approach

The solution follows a **Conformed Star Schema** architecture.

### Shared Dimensions

- Dim_Date
- Dim_Frequent_Flyer

### Fact Tables

- Fact_Flight_Activity
- Fact_Reservation
- Fact_Customer_Interaction

Benefits:

- Cross-functional reporting
- Consistent KPIs
- Simplified BI development
- Improved query performance

---

# 🗄 Physical Database Design

## Oracle Optimization Techniques

### Dimension Tables

- Date Dimension
- Frequent Flyer Dimension (SCD Type 2)
- Fare Basis Dimension
- Booking Channel Dimension
- Promotion Dimension
- Interaction Type Dimension
- Severity Dimension

### Fact Tables

- Fact_Flight_Activity
- Fact_Reservation
- Fact_Customer_Interaction

### Performance Enhancements

- Partitioned Fact Tables
- Bitmap Indexes
- Surrogate Keys
- Foreign Key Constraints
- Oracle Interval Partitioning

---

# 🔄 ETL Architecture

IBM InfoSphere DataStage orchestrates the complete ETL lifecycle.

## ETL Workflow

### Extract

- Oracle Enterprise Connectors
- Incremental extraction using timestamps
- Change Data Capture (CDC)

### Transform

- Business rule validation
- Data cleansing
- Type casting
- Null handling
- Surrogate key resolution

### Load

- Bulk loading into Oracle
- Partition-aware loading
- Optimized commit strategies

---

# 🔁 Slowly Changing Dimension (SCD Type 2)

The **DIM_FREQUENT_FLYER** table implements SCD Type 2 to preserve historical tier changes.

### Workflow

#### New Customer

- Generate surrogate key
- Insert current record
- Set `IS_CURRENT = 'Y'`

#### Tier Change

1. Expire current record
2. Set:
   - END_DATE
   - IS_CURRENT = 'N'

3. Insert new version:
   - New surrogate key
   - New START_DATE
   - IS_CURRENT = 'Y'

#### No Change

- Ignore record
- Reduce processing overhead

---

# 📅 Date Dimension Generation

The Date Dimension is automatically generated through DataStage.

### Derived Attributes

- Date Key (YYYYMMDD)
- Day Name
- Month Name
- Quarter
- Week Number
- Weekend Flag

### Benefits

- Eliminates manual maintenance
- Supports long-term planning horizons
- Ensures reporting consistency

---

# 📊 Data Marts

## Marketing Loyalty Mart

### Focus Areas

- Loyalty segmentation
- Promotion effectiveness
- Upgrade behavior

### KPIs

- Total Miles Earned
- Upgrade Rate
- Promotion Conversion Ratio

---

## Financial Yield Optimization Mart

### Focus Areas

- Revenue analysis
- Channel profitability
- Yield management

### KPIs

- Yield Per Seat
- Channel Margin
- Net Revenue Performance

---

# ⚙ Automation Framework

KPI calculations are parameter-driven.

### Advantages

- No schema modifications required
- Dynamic metric generation
- Easier maintenance
- Faster business rule updates

---

# ⏱ Job Scheduling & Dependencies

```text
Load_Dim_Date
       |
       v
Extract_Stg
       |
       v
+-------------------+
|                   |
v                   v
Load_Dim_SCD2   Load_Dim_Conf
|                   |
+---------+---------+
          |
          v
     Load_Facts
          |
          v
    Refresh_Marts
```

## Dependency Rules

### Parallel Dimension Loads

- Load_Dim_SCD2
- Load_Dim_Conf

Can execute simultaneously after staging completes.

### Fact Loading

Fact tables load only after successful completion of all dimension jobs.

---

# 🛡 Error Handling & Recovery

## Data Quality Management

### Reject Links

Invalid records are redirected to:

```text
QLY_ERR_QUARANTINE
```

Examples:

- Missing foreign keys
- Invalid formats
- Data type mismatches

---

## Recovery Strategy

### Checkpoint Restart

- Batch-based processing
- Resume from failure point
- Avoid full rollback

### Oracle Rollback Protection

- Transaction consistency
- Automatic rollback on critical failures

---

# 📈 Business Analytics Use Cases

The warehouse supports advanced analytical reporting.

## Frequent Flyer Analysis

- Tier distribution
- Flight activity
- Mileage accumulation

## Upgrade Propensity Analysis

- Fare basis performance
- Upgrade rates
- Booking class trends

## Revenue Analytics

- Gross revenue
- Net profit
- Channel profitability

## Promotion Effectiveness

- Campaign performance
- Booking conversions
- Discount impact

## Customer Support Analytics

- Resolution times
- CSAT scores
- Complaint severity trends

## Time-Series Analytics

- Monthly flight volume
- Mileage redemption trends
- Seasonal demand patterns

---

# 🚀 Technology Stack

| Component | Technology |
|------------|------------|
| Database | Oracle Database |
| ETL Tool | IBM InfoSphere DataStage |
| Data Modeling | Kimball Methodology |
| Data Warehouse | Star Schema |
| Scheduling | DataStage Director / Control-M |
| Indexing | Bitmap Indexes |
| Partitioning | Oracle Range Partitioning |

---

# 📌 Key Features

- Enterprise Data Warehouse Design
- Kimball Dimensional Modeling
- Oracle Performance Optimization
- SCD Type 2 Implementation
- Automated Date Dimension Generation
- DataStage ETL Pipelines
- Data Mart Architecture
- Error Handling & Recovery
- KPI Automation Framework
- Production-Ready Analytical Queries

---

# 📄 Deliverables

- Logical Data Model
- Physical Data Model
- Oracle DDL Scripts
- ETL Design Documentation
- DataStage Job Architecture
- Data Mart Specifications
- Scheduling & Dependency Design
- Analytical SQL Query Library

---

## Author

Designed as an enterprise-scale Airline Analytics Data Warehouse solution for supporting strategic decision-making across Marketing, Finance, and Customer Care domains.