notepad AGENTS.md
# AGENTS.md

## Project Name

Market Intelligence Platform

## Project Context

This is a GCP-based data engineering and BI portfolio project.

The goal is to build a market intelligence platform that combines:

1. Yelp Open Dataset
2. Zillow Research housing and rent data
3. BLS LAUS labor-market data

The platform will help answer market expansion questions such as:

- Which metro areas show strong business/customer activity?
- Which metros have rising housing or rent trends?
- Which metros have strong or improving labor-market conditions?
- Which categories are underserved by metro?
- Which businesses or categories show growth momentum?

## Current Project Status

Already completed outside this repo:

### Yelp

- Yelp Open Dataset uploaded to GCS.
- Yelp Bronze tables created in BigQuery.

Expected Yelp Bronze tables:

- business_bronze
- review_bronze
- checkin_bronze
- tip_bronze
- user_bronze

### Zillow

- Zillow Research datasets uploaded to GCS.
- Zillow Bronze tables created in BigQuery.

Expected Zillow Bronze tables:

- zhvi_bronze
- zori_bronze

### BLS

- BLS LAUS raw API JSON files uploaded to GCS.
- BLS data was ingested for metro-level locations.
- Four labor-market measures were ingested:
  - unemployment_rate
  - unemployed_persons
  - employed_persons
  - labor_force

Next immediate task:

- Flatten BLS raw JSON files from GCS into a BigQuery Bronze table.

## GCP Project Details

GCP Project ID:

```text
yelp-open-dataset

bls_datasets
zillow_research_data
yelp_open_dataset_json

raw_data_layer
yelp_bronze
zillow_bronze
bls_bronze
silver
gold