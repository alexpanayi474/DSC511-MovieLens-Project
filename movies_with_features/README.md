# Movies with Features

This is the final processed dataset ready for regression modeling, exported from `DSC_511_MovieLens.ipynb` after all EDA, feature engineering, and preprocessing steps. If you want to skip those steps and go straight to training the regression models, you can just read this file directly.

## Columns

| Column | Description |
|---|---|
| movieId | MovieLens movie ID |
| title | Movie title with release year |
| tmdb_id | TMDB identifier |
| genres | Pipe-separated genre labels from MovieLens |
| runtime | Runtime in minutes (nulls imputed using time-aware median) |
| release_date | Release date (validated against TMDB and IMDb) |
| original_language | ISO 639-1 language code |
| cast_frequency | Historical popularity score of the movie's top cast, computed only from movies released before it |
| director_frequency | Historical popularity score of the director, computed only from movies released before it |
| avg_rating | Simple average of all post-release ratings for the movie |
| num_ratings | Total number of post-release ratings |


