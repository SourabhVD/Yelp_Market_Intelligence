# Market Intelligence Platform

This project is a GCP-based data engineering and BI portfolio project for market expansion analysis. It combines Yelp business activity, Zillow housing and rent trends, and BLS LAUS labor-market indicators to help compare metro markets for go-to-market planning.

## Business Problem

Businesses evaluating expansion need more than one signal. A market can have strong customer activity but weak labor conditions, or attractive labor conditions but rising rent pressure. This platform is designed to bring those signals together so analysts can answer:

- Which metro areas show strong business and customer activity?
- Which metros have rising housing or rent trends?
- Which metros have strong or improving labor-market conditions?
- Which categories are underserved by metro?
- Which businesses or categories show growth momentum?

The end goal is a dashboard-ready set of market intelligence tables that support expansion prioritization, category opportunity analysis, and local market research.

## Current Status

The project is currently in the Bronze ingestion stage.

Completed outside this repo:

- Yelp Open Dataset raw files are uploaded to Google Cloud Storage.
- Yelp Bronze tables are created in BigQuery.
- Zillow Research raw files are uploaded to Google Cloud Storage.
- Zillow Bronze tables are created in BigQuery.
- BLS LAUS raw API JSON files are uploaded to Google Cloud Storage for metro locations.
- BLS LAUS raw files include four measures: `unemployment_rate`, `unemployed_persons`, `employed_persons`, and `labor_force`.

Next immediate task:

- Flatten BLS LAUS raw JSON files from GCS into a BigQuery Bronze table.

No new transformation code should be written until the BLS Bronze design and documentation are aligned.

## Data Sources

### Yelp Open Dataset

Yelp provides business, review, check-in, tip, and user data. In this platform it is used to measure local business presence, category mix, customer engagement, and review momentum.

Expected Bronze tables:

- `business_bronze`
- `review_bronze`
- `checkin_bronze`
- `tip_bronze`
- `user_bronze`

### Zillow Research

Zillow Research data provides housing and rent market indicators. The current plan uses home value and rent index data to measure affordability pressure and real estate trend direction by geography.

Expected Bronze tables:

- `zhvi_bronze`
- `zori_bronze`

### BLS LAUS

Bureau of Labor Statistics Local Area Unemployment Statistics data provides labor-market context for metro areas.

Raw GCS files already exist for:

- `unemployment_rate`
- `unemployed_persons`
- `employed_persons`
- `labor_force`

The next engineering step is to create the BLS Bronze table from these raw JSON files.

## GCP Architecture

Target GCP project:

```text
yelp-open-dataset
```

Storage and warehouse organization:

```text
Google Cloud Storage
  bls_datasets
  zillow_research_data
  yelp_open_dataset_json

BigQuery
  raw_data_layer
  yelp_bronze
  zillow_bronze
  bls_bronze
  silver
  gold
```

Planned flow:

```text
Source files/API extracts
  -> GCS raw storage
  -> BigQuery Bronze tables
  -> Silver cleaned and conformed models
  -> Gold dashboard-ready market intelligence tables
```

## Bronze, Silver, and Gold Plan

### Bronze

Bronze tables preserve source-level records with minimal transformation. They provide lineage, reloadability, and a stable base for validation.

Current Bronze status:

- Yelp Bronze: created.
- Zillow Bronze: created.
- BLS Bronze: next immediate task.

### Silver

Silver models will clean and standardize source fields, normalize dates, align metro geography, and prepare comparable monthly metrics across sources.

Planned Silver outputs include:

- Clean Yelp business and review tables.
- Yelp business-category bridge.
- Zillow monthly housing and rent metrics.
- BLS LAUS monthly labor metrics.
- Shared date and geography dimensions.

### Gold

Gold models will combine the Silver layer into business-ready analytical tables.

Planned Gold outputs include:

- Market opportunity by month.
- Business momentum by geography and business/category.
- Category demand and underserved-market indicators.

## Repository Structure

```text
configs/                     Project configuration files
dashboard/                   Metric definitions for dashboarding
docs/                        Architecture, data dictionary, validation, and project notes
ingestion/                   Source-specific ingestion scripts
orchestration/airflow_dags/  Airflow DAG for pipeline orchestration
sql/bronze_validation/       Bronze-layer validation SQL
sql/silver/                  Planned cleaned and standardized Silver models
sql/gold/                    Planned business-ready Gold models
tests/                       Python tests for validation logic
```

## Local Setup

Create and activate a virtual environment:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install dependencies:

```powershell
pip install -r requirements.txt
```

Run tests:

```powershell
pytest
```
