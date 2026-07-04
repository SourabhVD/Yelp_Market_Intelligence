# Data Dictionary

This dictionary documents the planned data assets for the Market Intelligence Platform. The project is currently focused on completing the Bronze layer before implementing downstream Silver and Gold transformations.

## Source Systems

| Source | Purpose | Current Status |
| --- | --- | --- |
| Yelp Open Dataset | Business presence, categories, reviews, ratings, customer engagement, and local business momentum | Raw data uploaded to GCS; Bronze tables created in BigQuery |
| Zillow Research | Housing value and rent trend signals for market affordability and demand pressure | Raw data uploaded to GCS; Bronze tables created in BigQuery |
| BLS LAUS | Metro-level labor-market indicators | Raw API JSON files uploaded to GCS; Bronze transformation is next |

## Yelp Bronze

Expected BigQuery dataset: `yelp_bronze`

| Table | Grain | Description | Status |
| --- | --- | --- | --- |
| `business_bronze` | One row per Yelp business | Raw business attributes, location fields, categories, ratings, and review counts | Created |
| `review_bronze` | One row per Yelp review | Raw review text, rating, user, business, and review date fields | Created |
| `checkin_bronze` | One row per Yelp check-in record | Raw check-in activity by business | Created |
| `tip_bronze` | One row per Yelp tip | Raw short-form user tips by business | Created |
| `user_bronze` | One row per Yelp user | Raw user profile and activity attributes | Created |

Important business signals:

- Business count by metro and category.
- Review volume and rating trends.
- Category concentration and gaps.
- Customer engagement momentum.

## Zillow Bronze

Expected BigQuery dataset: `zillow_bronze`

| Table | Grain | Description | Status |
| --- | --- | --- | --- |
| `zhvi_bronze` | Zillow geography and time-series values | Zillow Home Value Index source data | Created |
| `zori_bronze` | Zillow geography and time-series values | Zillow Observed Rent Index source data | Created |

Important business signals:

- Home value level and trend.
- Rent level and trend.
- Housing affordability pressure.
- Real estate demand context by geography.

## BLS LAUS Raw Files

Expected raw storage: `bls_datasets`

The BLS LAUS raw API JSON files have already been uploaded to GCS for metro-level locations.

Available measures:

| Measure | Description | Current Status |
| --- | --- | --- |
| `unemployment_rate` | Percentage of labor force unemployed | Raw JSON uploaded to GCS |
| `unemployed_persons` | Number of unemployed persons | Raw JSON uploaded to GCS |
| `employed_persons` | Number of employed persons | Raw JSON uploaded to GCS |
| `labor_force` | Total civilian labor force | Raw JSON uploaded to GCS |

## BLS Bronze Plan

Expected BigQuery dataset: `bls_bronze`

The next immediate task is to flatten the BLS LAUS raw API JSON files into a Bronze table.

Planned table:

| Table | Grain | Description | Status |
| --- | --- | --- | --- |
| `laus_bronze` | One row per metro, measure, year, and period | Flattened LAUS observations from raw API JSON files, including source metadata | Planned next |

Planned core fields:

| Field | Description |
| --- | --- |
| `series_id` | BLS LAUS series identifier from the source response |
| `area_code` | BLS geography or area code when available |
| `area_name` | Metro or area name when available |
| `measure` | Normalized measure name, such as `unemployment_rate` |
| `year` | Observation year |
| `period` | BLS period code |
| `period_name` | Human-readable period name when available |
| `month` | Parsed month value for monthly observations |
| `value` | Numeric observation value |
| `source_file` | GCS object or file name used to load the row |
| `loaded_at` | Timestamp when the row was loaded into BigQuery |

Validation expectations:

- All four measures are present.
- Metro coverage matches the ingested raw files.
- Year and period coverage matches the API responses.
- Numeric values parse successfully.
- Source file lineage is retained.

## Silver Plan

Expected BigQuery dataset: `silver`

| Model | Grain | Purpose | Status |
| --- | --- | --- | --- |
| `dim_date` | One row per date or month | Shared calendar fields for monthly analysis | Planned |
| `dim_geography` | One row per standardized geography | Shared metro/geography mapping across Yelp, Zillow, and BLS | Planned |
| `yelp_business_clean` | One row per business | Cleaned Yelp business attributes and geography fields | Planned |
| `yelp_review_clean` | One row per review | Cleaned Yelp review facts | Planned |
| `yelp_business_category_bridge` | One row per business/category pair | Normalized Yelp category relationships | Planned |
| `zillow_monthly_metrics` | One row per geography, month, and Zillow metric | Standardized ZHVI and ZORI monthly values | Planned |
| `bls_laus_monthly` | One row per metro, month, and labor measure set | Standardized LAUS monthly labor metrics | Planned after BLS Bronze |

## Gold Plan

Expected BigQuery dataset: `gold`

| Model | Grain | Purpose | Status |
| --- | --- | --- | --- |
| `market_opportunity_monthly` | One row per metro and month | Combines Yelp, Zillow, and BLS signals into market opportunity metrics | Planned |
| `business_momentum` | One row per business or business/month | Measures Yelp activity and engagement momentum | Planned |
| `category_demand` | One row per metro, category, and period | Identifies category demand and underserved-market signals | Planned |

## Business Metrics

Planned analytical metrics include:

- Business density by metro and category.
- Review volume and rating trend.
- Category concentration and underserved-category indicators.
- Home value and rent trend direction.
- Unemployment rate and employment trend.
- Labor force size and change over time.
- Composite market opportunity score.

The immediate dependency for these metrics is the completion and validation of the BLS Bronze table.
