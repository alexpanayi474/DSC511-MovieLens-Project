# MovieLens + TMDB Enriched Dataset

Enriched version of the MovieLens ml-latest dataset (86,537 movies) with metadata from the TMDB API.

## Files
- `movies_enriched_selected.csv` — All movies from movies.csv merged with TMDB metadata

## Enriched Columns
| Column | Description |
|--------|-------------|
| movieId | MovieLens movie ID |
| title | Movie title with year |
| genres | Pipe-separated genres from MovieLens |
| tmdb_id | TMDB identifier |
| budget | Production budget (USD) |
| revenue | Box office revenue (USD) |
| runtime | Runtime in minutes |
| release_date | Release date (YYYY-MM-DD) |
| original_language | ISO 639-1 language code |
| director | Director(s), pipe-separated |
| top_cast | Top 3 cast members, pipe-separated |
| production_companies | Pipe-separated |
| production_countries | Pipe-separated |
| keywords | TMDB keywords, pipe-separated |

## Notes
- 1,333 movies have no TMDB match (no tmdbId in links.csv or TMDB returned 404)
- Budget/revenue missing for ~70K movies (not reported on TMDB)
- Keywords missing for ~25K movies (not tagged on TMDB)
- TMDB data fetched via API on May 2026
