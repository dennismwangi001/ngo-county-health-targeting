# County Health Targeting & Data-Quality Review: Kenya

**Industry:** NGO / Public Health (Kenya)
**Tools:** Python (pandas) · Power BI (planned)
**Status:** In progress. Data audit complete; cleaning and analysis under way.

## Business Problem
A health-focused NGO has a fixed budget to expand maternal and child
health programming across Kenya's counties. Leadership must decide which
counties to prioritize, based on gaps in immunization coverage, use of
skilled care, and child feeding practices, and must know how far the
county-level figures can be trusted, given survey sampling limits.

## Business Questions
1. Which counties have the lowest immunization coverage, and how far
   below the national figure are they?
2. Where do low immunization and low use of skilled care (antenatal
   care, delivery assistance, place of delivery) overlap in the same
   counties? The data measures use of care, not barriers such as
   distance or cost.
3. Which counties improved or stalled between the 2014 and 2022
   surveys? Immunization has county data for both rounds; other
   indicators depend on data availability (see Data Quality Findings).
4. How reliable is each county's estimate? Which county figures are too
   uncertain to drive a funding decision?
5. What is a defensible priority ranking of counties, and how much does
   it change if the weighting changes?

## Data Source
Three county-level extracts from the Demographic and Health Surveys
(DHS) Program, downloaded from the Humanitarian Data Exchange (HDX),
dataset "Kenya - Subnational Demographic and Health Data":

- `access-to-health-care_subnational_ken.csv` (10,000 rows)
- `immunization_subnational_ken.csv` (2,208 rows)
- `iycf_subnational_ken.csv` (571 rows; infant and young child feeding)

Survey rounds cover 1989 to 2022. The files are in long format: one row
is one indicator for one location, survey year and population breakdown,
with fields for the value, weighted and unweighted sample sizes, and
confidence limits.

Source: The DHS Program (ICF), via HDX. Terms of use: [add from the HDX
dataset page]. Raw files are not committed to this repo; download them
from the link above.

## Data Quality Findings
- **The access-to-care extract is truncated.** It stops at exactly
  10,000 rows, sorted by survey year, and ends mid-indicator in 2014
  (the final indicator has 35 rows where its sibling indicators have
  110). Fever and diarrhea treatment indicators and any 2022 rows are
  missing. Two "Other health worker" categories are probably relabeled
  "Community health worker" in 2014 rather than cut off.
- **County-level estimates start in 2014.** Earlier rounds report 7 to 8
  regions (the former provinces). Immunization has county data for 2014
  and 2022, the access extract for 2014 only, and IYCF for 2022 only.
- **Counties and provinces are mixed.** 2014 and 2022 files list 54
  locations: 47 counties plus 7 former provinces, which overlap the
  counties and would double-count if ranked together.
- **Confidence intervals are empty** (100% missing) in all three files,
  so reliability is judged from unweighted sample size instead.
- **Sample sizes are small.** For 2022 immunization, the unweighted
  sample per county rate estimate ranges from 39 to 160 children
  (median 74).
- **IYCF sample sizes are missing for one indicator only:** "Median
  duration of any breastfeeding", a duration in months rather than a
  percentage.
- **2022 immunization is clean after filtering:** all 1,026 rows are
  flagged as preferred, and the county rows give exactly one row per
  county and indicator.

## Cleaning Rules
1. Keep county-level rows only; drop the 7 former provinces (Central,
   Coast, Eastern, North Eastern, Nyanza, Rift Valley, Western).
2. Keep rows flagged `IsPreferred = 1`, and verify one row per county,
   indicator and survey year.
3. Keep rate indicators only; drop count rows ("Number of ..."),
   component and total rows, and "don't know or missing" categories.
4. Exclude "Median duration of any breastfeeding" from rate analysis.
5. Record each indicator's direction (for example, "Received no
   vaccinations" is worse when higher).
6. Check that county names match across the three files and any
   boundary file used for maps.

## Methodology
1. Data audit (done): structure, coverage, completeness, reliability.
2. Cleaning: apply the rules above and save processed tables.
3. County ranking by immunization (2022), against a national figure
   derived from county values weighted by sample size. This is a derived
   estimate, not an official one.
4. Reliability: approximate 95% margins of error from unweighted sample
   size. For a 70% estimate, roughly ±7 points at n=160, ±10 at n=74 and
   ±14 at n=39. This assumes simple random sampling and understates the
   true uncertainty, because DHS uses a clustered design.
5. Composite priority index with a sensitivity check on the weights.
6. Power BI dashboard (planned).

## Key Insights
*To be added as each question is answered.*

## Business Recommendations
*To be added once the insights are in.*

## Limitations
- Survey estimates for small areas, not routine program records.
- The access indicators measure use of care, not barriers to it.
- Access data may be limited to 2014, so overlap analysis with 2022
  immunization may not be like-for-like.
- County margins of error are approximate (see Methodology).

## Progress
- [x] Download and load the three DHS extracts
- [x] Audit structure, coverage and data quality
- [ ] Confirm whether 2022 access data can be re-pulled
- [ ] Apply cleaning rules and save processed tables
- [ ] Answer Questions 1 to 5
- [ ] Build the dashboard
- [ ] Add insights and recommendations

## Repo Structure
- data/        raw (not committed) and processed data
- sql/         SQL scripts, if used for the analysis
- notebooks/   Python analysis notebooks
- dashboard/   Power BI files and exported visuals
- docs/        supporting notes and data dictionary

## How to Reproduce
1. Download the three CSVs from the HDX dataset page above into
   `data/raw/`.
2. Run `notebooks/01_data_exploration.ipynb` to audit the files.
3. Further notebooks will be added as the analysis progresses.