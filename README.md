# MovieLens Project

**Course:** DSC 511 Big Data Analytics

**Team Members:** Maria Michaelidou, Thanasis Kalos, Alexandros Panayi


## Overview

This project applies Big Data Analytics to the MovieLens ml-latest dataset, which contains 33,832,162 ratings across 86,537 movies by 330,975 users (1995 to 2023). Using Apache Spark, we perform exploratory data analysis, build regression models to predict movie ratings, and develop a collaborative filtering recommendation system. Movie metadata was further enriched via the TMDB API, adding fields such as runtime, director, cast, budget, and release date.


## Repository Structure

**DSC_511_MovieLens.ipynb** covers data ingestion, EDA, feature engineering, target variable construction, and ML regression models.

**TMDB_Enrichment_for_MovieLens.ipynb** fetches metadata from the TMDB API and produces movies_enriched_selected.csv.

**ALS_Recomendation.ipynb** implements the collaborative filtering recommendation system using ALS.

**movies_enriched_selected.csv** is the TMDB-enriched movie dataset used as input for the main notebook.


## Note on AI Tool Usage

The TMDB enrichment notebook was developed with AI assistance (Claude by Anthropic) for the parts involving concurrent API calls and checkpoint-based recovery, as these are not covered in the DSC 511 curriculum. All generated code was reviewed, tested, and verified by the team against the TMDB website before inclusion. The analysis design, data joining logic, and Spark integration were done entirely by us.


## Analysis Workflow

Data is loaded into Spark DataFrames, cleaned, and joined into a single working DataFrame. The EDA examines rating distributions, movie release trends, platform activity over time, user behavior, and genre patterns across decades. For the regression task, the prediction target is the weighted average rating per movie, computed using Bayesian smoothing to stabilize scores for movies with few ratings: weighted_avg_rating = (v / (v + m)) * R + (m / (v + m)) * C, where v is the number of ratings, R is the movie average, C is the global mean (3.53), and m is 27 (the median number of ratings). This is preferred over the raw average because it prevents low-count movies from distorting model training. Features include genre encoding, original language, release month, runtime, and time-aware cast and director frequency scores. Three models are trained chronologically: Linear Regression, Random Forest, and Gradient Boosted Trees. The recommendation system uses ALS with a 60/20/20 split, a baseline model, and hyperparameter tuning via 5-fold cross-validation on a training sample.


## Key Findings

Rating patterns show 4.0 as the most common score, with whole-star ratings more frequent than half-star ones. Movie catalog growth peaked around 2015 to 2018, and platform activity tracked industry milestones including the DVD boom, the Netflix era, and the rise of streaming. User activity is highly skewed toward a small group of power users who tend to rate lower than the average and favor Drama, Comedy, and Thriller. Film-Noir is the highest-rated genre historically while Horror is the lowest and declining. Older decades score higher due to survivorship bias, with the exception of 1980s films which have seen a recent nostalgia-driven recovery.

On the regression task, Gradient Boosted Trees outperformed Random Forest and Linear Regression on both target variables, with the weighted_avg_rating target producing more stable results than the raw average. For the recommendation system, the tuned ALS model improved on the baseline and produced top-10 recommendations that were genre-consistent with each user's rating history.


## Running the Project

Run the notebooks in this order: TMDB_Enrichment_for_MovieLens.ipynb first (requires a TMDB API key), then DSC_511_MovieLens.ipynb, then ALS_Recomendation.ipynb. All notebooks expect ratings.csv, movies.csv, and links.csv to be available in the configured Google Drive path.


## Data Sources

MovieLens dataset: https://grouplens.org/datasets/movielens/latest

TMDB API: https://www.themoviedb.org/documentation/api
