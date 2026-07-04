# Architecture

## Purpose

The Market Intelligence Platform is designed to combine business activity, housing and rent trends, and labor-market conditions into a single analytics environment for metro-level expansion analysis.

The business problem is to help decision-makers identify attractive markets for go-to-market investment by comparing:

- Local business and customer activity from Yelp.
- Housing value and rent pressure from Zillow.
- Employment and unemployment conditions from BLS LAUS.

## Current Project Status

Completed outside this repo:

- Yelp raw data is uploaded to GCS.
- Yelp Bronze tables are created in BigQuery.
- Zillow raw data is uploaded to GCS.
- Zillow Bronze tables are created in BigQuery.
- BLS LAUS raw API JSON files are uploaded to GCS for metro locations.
- BLS LAUS raw files cover `unemployment_rate`, `unemployed_persons`, `employed_persons`, and `labor_force`.

Next immediate task:

- Build the BLS Bronze transformation that flattens raw BLS LAUS JSON files from GCS into BigQuery.

## GCP Components

GCP project:

```text
yelp-open-dataset
```

Cloud Storage buckets or raw storage areas:

```text
bls_datasets
zillow_research_data
yelp_open_dataset_json
```

BigQuery datasets:

```text
raw_data_layer
yelp_bronze
zillow_bronze
bls_bronze
silver
gold
```

## Logical Data Flow

```text
Yelp Open Dataset files
Zillow Research files
BLS LAUS API JSON extracts
        |
        v
Google Cloud Storage raw landing areas
        |
        v
BigQuery Bronze datasets
        |
        v
Silver cleaned and conformed models
        |
        v
Gold market intelligence models
        |
        v
Dashboard metrics and analysis
```

## Layer Responsibilities

### Raw Storage

Raw storage keeps the original source files or API extracts in GCS. This gives the project a reloadable source of truth and keeps ingestion separate from transformation.

Current raw storage status:

- Yelp raw files are present in GCS.
- Zillow raw files are present in GCS.
- BLS LAUS raw API JSON files are present in GCS for metro locations and four measures.

### Bronze

Bronze tables are source-aligned BigQuery tables. They should preserve raw fields, source identifiers, and load metadata with minimal interpretation.

Current Bronze status:

- `yelp_bronze` exists for Yelp source tables.
- `zillow_bronze` exists for Zillow source tables.
- `bls_bronze` is planned next.

The BLS Bronze table should flatten LAUS JSON records into tabular rows while retaining enough metadata to trace each row back to source measure, geography, period, and ingestion file.

### Silver

Silver models will clean, type, and standardize the Bronze data. This layer should make sources comparable across geography and time.

Planned responsibilities:

- Normalize dates to month-level fields where appropriate.
- Standardize metro/geography identifiers and names.
- Clean Yelp business, review, and category fields.
- Convert Zillow wide time-series files into monthly metric rows.
- Convert BLS LAUS measures into monthly labor-market metrics.
- Create shared date and geography dimensions.

### Gold

Gold models will be dashboard-ready analytical tables that combine Silver source models into business-facing metrics.

Planned outputs:

- Monthly market opportunity table.
- Business momentum table.
- Category demand and underserved-market table.

## Immediate Engineering Focus

The next task is BLS Bronze transformation:

1. Read BLS LAUS raw JSON files from GCS.
2. Flatten metro-level observations for the four ingested measures.
3. Load the results into a BigQuery table in `bls_bronze`.
4. Preserve source metadata for validation and lineage.
5. Validate row counts, measure coverage, metro coverage, and date coverage.

No Silver or Gold implementation should proceed until the BLS Bronze table is available and validated.
