# MovieLens Project

**Course:** DSC 511 - Big Data Analytics

**Team:** Maria Michaelidou, Thanasis Kalos, Alexandros Panayi


## Overview

This project uses Apache Spark to analyze the MovieLens ml-latest dataset (33.8M ratings, 86,537 movies, 330,975 users, spanning 1995 to 2023). We do exploratory data analysis, build regression models to predict movie ratings, and implement a collaborative filtering recommendation system with ALS. Movie metadata was enriched through the TMDB API, adding runtime, director, cast, budget, revenue, release date, and more for ~85,000 movies.


## Repository Structure

- `DSC_511_MovieLens.ipynb` - Data ingestion, EDA, feature engineering, and ML regression models
- `TMDB_Enrichment_for_MovieLens.ipynb` - Fetches metadata from the TMDB API, produces `movies_enriched_selected.csv`
- `ALS_Recomendation.ipynb` - Collaborative filtering recommendation system using ALS
- `movies_enriched_data/movies_enriched_selected.csv` - The enriched movie dataset used as input for the main notebook
- `movies_with_features/movies_with_features.csv` - Final version of the data right before the regression step, with all features already computed. Useful if you want to skip the EDA/engineering/preprocessing and jump straight to model training


## Analysis Workflow

Data is loaded into Spark DataFrames, cleaned, and joined into a single working DataFrame. The EDA covers rating distributions, movie release trends, platform activity over time, user behavior, and genre patterns across decades.

For the regression task, the target is the weighted average rating per movie, computed with Bayesian smoothing:

`weighted_avg_rating = (v / (v + m)) * R + (m / (v + m)) * C`

where v = number of ratings, R = movie's raw average, C = global mean (2.9987), and m = 27 (median number of ratings per movie). This stabilizes scores for movies with few ratings and prevents them from distorting model training.

Features used (40-dimensional vector): genre encoding (CountVectorizer), original language, release month, runtime, and time-aware cast/director frequency scores (computed only from movies released before each film, to avoid data leakage).

Three models were trained using a chronological split (pre-2019 train, 2019-2020 validation, 2021+ test): Linear Regression, Random Forest, and Gradient Boosted Trees.

The recommendation system uses ALS on a random 60/20/20 split (20.3M / 6.8M / 6.8M ratings), with a baseline model followed by hyperparameter tuning via 5-fold cross-validation.


## Results

### Regression

We initially trained all three models using the simple average rating as the target. Results were poor across the board (e.g. Linear Regression RMSE = 0.8854, Random Forest RMSE = 0.9221). The problem is that raw averages are unreliable for movies with very few ratings, so the target itself is noisy.

We then switched to the Bayesian-smoothed weighted average rating, which pulls low-count movies toward the global mean. This made a huge difference:

| Model | RMSE | MAE |
|---|---|---|
| Linear Regression | 0.1764 | 0.1231 |
| Random Forest | 0.1714 | 0.1170 |
| Gradient Boosted Trees | 0.1727 | 0.1210 |

Random Forest performed best, so we tuned it (grid search over numTrees and maxDepth). Best config: numTrees=50, maxDepth=10 with validation RMSE of 0.1690.

**Final test set performance (tuned Random Forest): RMSE = 0.1564, MAE = 0.1113**

### ALS Recommendation System

| Model | RMSE | MAE |
|---|---|---|
| Baseline (rank=10, regParam=0.01, maxIter=10) | 0.8461 | 0.6348 |
| Tuned (rank=10, regParam=0.1, maxIter=15) | 0.8112 | 0.6237 |

The tuned model was selected through 5-fold CV over 12 hyperparameter combinations. Cold start affected less than 0.15% of validation/test ratings.

**Final test set performance (tuned ALS): RMSE = 0.8116, MAE = 0.6239**

Top-10 recommendations were genre-consistent with each user's rating history.


## Key Findings

4.0 is the most common rating, and whole-star ratings are more frequent than half-star ones. Movie catalog growth peaked around 2015-2018, and platform activity tracked industry milestones (DVD boom, Netflix era, rise of streaming). User activity is heavily skewed toward a small group of power users who tend to rate lower than average and favor Drama, Comedy, and Thriller.

Film-Noir is the highest-rated genre historically; Horror is the lowest and declining. Older decades score higher due to survivorship bias, except for 1980s films which have seen a nostalgia-driven recovery.


## TMDB Enrichment

Out of 86,537 movies, 85,204 were successfully matched and enriched via the TMDB API. Fields fetched: budget, revenue, runtime, release_date, original_language, director, top_cast, production_companies, production_countries, and keywords.

We noticed inconsistencies between the release years in MovieLens movie titles and the dates returned by TMDB, so we validated those cases by also fetching release dates from the IMDb API. This resolved 5,343 mismatched dates and improved the overall quality of the release_date column.


## Note on AI Tool Usage

The TMDB enrichment notebook was developed with AI assistance (Claude by Anthropic) for the parts involving concurrent API calls and checkpoint-based recovery, as these are not covered in the DSC 511 curriculum. All generated code was reviewed, tested, and verified by the team against the TMDB website before inclusion.


## Running the Project

Run the notebooks in order:

1. `TMDB_Enrichment_for_MovieLens.ipynb` (requires a TMDB API key)
2. `DSC_511_MovieLens.ipynb`
3. `ALS_Recomendation.ipynb`

All notebooks expect `ratings.csv`, `movies.csv`, and `links.csv` to be available in the configured Google Drive path.


## Data Sources

- MovieLens ml-latest: https://grouplens.org/datasets/movielens/latest
- TMDB API: https://www.themoviedb.org/documentation/api
