# CFPB HMDA Mortgage EDA — NYC Denial Pattern Analysis

## Project Overview
Exploratory data analysis of 506,000+ mortgage loan applications in New York State using CFPB's Home Mortgage Disclosure Act (HMDA) dataset. The project surfaces denial patterns across income bands, race, loan purpose, and geography using a SQL-first analytics approach.

**Live Dashboard:** https://public.tableau.com/app/profile/mahfuz.kabir.pulak/viz/HMDA_Mortgage_EDA/CFPBMortgage-DenialPatternAnalysis

---

## Key Findings
- Applicants earning **under $50K** face a **19.86% denial rate** — nearly 5x higher than the $50K–$100K income band (4.40%)
- Applicants identifying as **"2 or more minority races"** face a **64.25% denial rate** vs **36.13%** for White applicants
- **70% of all denials** are on Home Purchase loans — the primary path to homeownership
- High-income counties (tract income >$100K) still show **35–40% denial rates**, suggesting income alone doesn't explain denial patterns

---

## Data Source
- **API:** CFPB HMDA Data Browser API (`ffiec.cfpb.gov/v2/data-browser-api`)
- **Coverage:** New York State, 2022 filing year
- **Actions included:** Originated (1), Approved Not Accepted (2), Denied (3)
- **Volume:** 506,536 loan applications across 99 counties

---

## Tech Stack
| Layer | Tool |
|---|---|
| Data source | CFPB HMDA REST API |
| Storage | Snowflake (free tier) |
| Transformation | Snowflake SQL — multi-CTE mart views |
| Visualization | Tableau Public |

---

## SQL Architecture

**RAW schema**
- `lc_loans` — 506K rows, 99 columns

**MART schema**
- `v_staging` — casting, cleaning, income bucketing, outcome flags
- `v_denial_profile` — denial rate by income × race × loan purpose
- `v_geo_summary` — county-level denial rate, volume, tract income
- `v_borrower_profile` — applicant demographics × loan outcomes
- `v_denial_reasons` — primary denial reason distribution
````

All mart views follow a consistent multi-CTE pattern:
1. `staging` CTE — pulls from raw, casts types, creates derived fields
2. Analytical CTE — applies business logic and aggregations
3. Final SELECT — clean output for BI layer

---

## Dashboard
6-panel Tableau Public dashboard covering:
- KPI summary (total applications, denials, avg denial rate, avg loan amount)
- Denial rate by income bucket
- Denial rate by race
- Denials by loan purpose
- County-level treemap (denial rate × application volume)
- Scatter plot: tract income vs denial rate by county

---

## Repository Structure

cfpb-hmda-mortgage-eda/
├── README.md
└── sql/
├── 01_raw_ddl.sql
├── 02_file_format.sql
├── 03_v_staging.sql
├── 04_v_denial_profile.sql
├── 05_v_geo_summary.sql
├── 06_v_borrower_profile.sql
└── 07_v_denial_reasons.sql

---

## How to Reproduce
1. Create a free Snowflake trial account
2. Download HMDA data via API: `https://ffiec.cfpb.gov/v2/data-browser-api/view/csv?years=2022&states=NY&actions_taken=1,2,3`
3. Run SQL files in order (01 → 07)
4. Connect Tableau Public to exported CSVs from mart views
