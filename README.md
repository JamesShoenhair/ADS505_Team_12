<img width="1088" height="581" alt="Screenshot 2026-10-19 at 6 16 11 AM" src="https://github.com/user-attachments/assets/52db144c-10ff-40ab-aea4-2738fc5922c4" />
# Screening and Ranking Emerging Markets for M&A Using Macroeconomic Indicators

**ADS-505: Applied Data Science for Business | University of San Diego | Team 12**
Dana Neuman, Jordyn Gonzales, James Shoenhair

## Purpose

Corporate development and M&A teams need to decide which emerging-market countries are the most attractive places to search for acquisitions. That screening is usually manual, slow, and inconsistent across analysts. This project builds a transparent, repeatable, top-down country screen. It ranks 155 emerging and developing economies by their likelihood of outgrowing their peers over the next three years, using IMF World Economic Outlook (WEO) indicators.

The screen answers the first question in an M&A search, *where to look*. Industry selection and target-level valuation (DCF, synergies) come after it.

**Research questions**

1. Do macroeconomic indicators identify future top-growth emerging markets better than recent growth alone?
2. Which indicators contribute most to identifying those markets?
3. How closely does the model's 2026–2028 ranking agree with a ranking based on the IMF's own projections?

**Prediction target:** a country-year is labeled 1 if its average annual real GDP growth (year-over-year %) over the three years after the indicator year ranks in the top 33% of emerging markets that year, and 0 otherwise. The model's predicted probability is used to rank countries.

## Repository Structure

```
ADS505_Team_12/
├── README.md                       # Project overview, setup, and run instructions
├── ads505final.csv                 # IMF WEO extract used by the notebook
└── ADS505_Final_Project_final.ipynb   # Technical notebook (full analysis)
```

## Data

**Source:** International Monetary Fund. (2026). *World Economic Outlook database: April 2026 edition* [Data set]. https://www.imf.org/en/Publications/WEO/weo-database/2026/April

**File:** `ADS505_IMF_rawdata.csv`, 1,288 rows x 22 columns, an extract of the WEO database covering 200 countries and aggregates, seven indicators, and annual values for 2017–2031. Values for 2017–2025 are actuals or IMF estimates; 2026–2031 are IMF projections.

| Indicator | Notebook name |
|---|---|
| GDP growth, constant prices (% change) | `gdp_growth_pct` |
| GDP, current prices (PPP, international $) | `gdp_ppp_bn` |
| GDP per capita (PPP, international $) | `gdp_per_capita_ppp` |
| GDP price deflator (index) | `gdp_deflator_idx` |
| General government net debt (% of GDP) | `net_debt_pct_gdp` |
| General government net lending/borrowing (% of GDP) | `net_lending_pct_gdp` |
| Current account balance (% of GDP) | `current_account_pct_gdp` |

**To recreate the extract:** from the WEO database link above, select all countries and the seven indicators listed, for 2017–2031, and export to CSV.

## Setup

Requires Python 3.11 or later.

```bash
git clone https://github.com/JamesShoenhair/ADS505_Team_12.git
cd ADS505_Team_12
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install numpy pandas matplotlib seaborn scikit-learn scipy jupyter
```

## How to Run

1. Launch Jupyter from the repository folder: `jupyter notebook`
2. Open `ADS505_Final_Project_Final.ipynb`.
3. Select **Kernel > Restart & Run All**.

The notebook loads `ADS505_IMF_rawdata.csv` directly from this GitHub repository, so an internet connection is required. It runs end-to-end in a few minutes on a standard laptop, and a fixed random seed (`SEED = 1`) makes results reproducible.

## Method Summary

1. **Cleaning and reshaping:** removed regional aggregates and the 41 IMF-classified advanced economies; reshaped to one row per country per year.
2. **Pre-processing:** dropped net debt (over 55% missing); converted the GDP deflator to year-over-year inflation; log-transformed GDP and GDP per capita; added growth momentum and growth relative to the emerging-market median; winsorized features at the 1st and 99th percentiles.
3. **Temporal split:** trained on base years 2018–2020, tuned on 2021, and tested once on 2022. The split is by year rather than random, so the model never learns from the future.
4. **Models:** logistic regression, linear discriminant analysis, and random forest, compared against a naive baseline that ranks countries by current-year growth. Winsorizing and scaling are fit inside a pipeline on training data only.
5. **Metrics:** ROC-AUC (overall ranking quality) and precision at 10 (share of the top-10 shortlist that were actual top growers).

## Key Results

| Model | Validation ROC-AUC (2021) | Test ROC-AUC (2022) |
|---|---|---|
| Baseline (current growth) | 0.564 | 0.715 |
| Logistic regression (final) | 0.626 | 0.669 |
| LDA | 0.628 | n/a |
| Random forest | 0.595 | n/a |

- All three models beat the baseline on validation, but the baseline beat the final model on the held-out test year. Current growth carried most of the predictive signal.
- Lower GDP per capita was the strongest predictor of future top growth (economic convergence), so the model's top ranks favor low-income and fragile economies that an acquirer would likely screen out on risk.
- The model's 2026–2028 ranking moderately agrees with an IMF-projection ranking (Spearman = 0.59). Guinea and Uganda appear in both top-10 lists.

See the Model Brief and Discussion sections of the notebook for limitations and recommended next steps.

## Team Contributions

| Member | Contributions |
|---|---|
| Dana Neuman | *Predictive Models, Forecast Models, Initial final model selection and final recommendations/results, initial business model draft, initial README draft, contributing to AI reflection log and designing final project slides/speaking to 2 slides* |
| Jordyn Gonzales | *All of EDA and Pre-Processing, contributing to AI Reflection Log, finalizing business Brief EDA/Pre-processing section, 2 presentation slides* |
| James Shoenhair | *Predictive Models (extending LDA), editing forecast model and final model selection to LDA after updated shrinkage results, editing notebook for any errors/ updating final results and recommendations with our finalized data, editing business brief with our updated data and model selection/recommendations, contributing to AI reflection log Updating/finalizing README file and ensuring no issues with our Github repository settings, 2 presentation slides* |

## AI Assistance Disclosure

### Tools Used: 
- Claude
- ChatGPT

- AI tools supported developing statistical models baseline, logistic regression, and linear discriminant analysis. 
- AI tools supported developing and editing time series analysis of future years (2026-2028). 

- To review prompts, suggessted outputs, and how those outputs were reviewed and corrected, please see the AI reflection log in the notebook.

## References

International Monetary Fund. (2026). *World Economic Outlook database: April 2026 edition* [Data set]. https://www.imf.org/en/Publications/WEO/weo-database/2026/April

O'Neill, J. (2001). *Building better global economic BRICs* (Global Economics Paper No. 66). Goldman Sachs.
