# Netflix Content Analysis

Cleaning and analysing the Netflix Movies and TV Shows dataset with Pandas, then merging in IMDb ratings to explore how content is rated.

## Dataset

- [Netflix Movies and TV Shows](https://www.kaggle.com/datasets/shivamb/netflix-shows): listings of Netflix movies and TV shows (8,807 rows, 12 columns; 8,790 rows after cleaning), including cast, country, rating and duration.
- [Netflix - All Movie Ratings by IMDb](https://www.kaggle.com/datasets/sukhmandeepsinghbrar/netflix-all-movie-ratings-by-imdb): IMDb ratings for movies on Netflix, merged in to explore ratings by genre.

## Questions I wanted to answer

- How many titles were added to Netflix each year?
- What is the split between movies and TV shows?
- Which genres are the most common?
- Which countries produce the most content?
- How have content ratings (e.g. TV-MA) changed over time?
- Which genres have the highest IMDb ratings?

## Data Cleaning

- **Missing values:**
  - `director`, `cast` and `country` were filled with `'Unknown'`. Dropping those rows would have removed a large share of the data (`director` alone was missing in ~30% of rows).
  - `date_added`, `rating` and `duration` were missing in only a handful of rows, so those rows were dropped.
- **`date_added`:** converted to a real datetime so it can be grouped by year. Some values had a leading space that made the conversion fail, so whitespace was stripped first.
- **`duration`:** it mixed minutes (movies) and seasons (TV shows), so it was split into `duration_minutes` and `duration_seasons`. Non-applicable rows are left as NaN rather than 0, because 0 would wrongly suggest a real value.
- **Genres and countries:** `listed_in` and `country` hold several comma-separated values in one cell. Splitting and exploding gives one row per genre/country, so counts per category are accurate. The exploded versions are separate DataFrames, so the main one keeps one row per title. For `country`, stray leading/trailing commas were stripped first, because a few entries such as `", France, Algeria"` otherwise produced malformed country names.
- **IMDb merge:** the IMDb data had duplicate titles, so ratings were averaged per title before merging (otherwise the merge created extra rows). A left merge keeps every Netflix title; titles with no IMDb match get NaN.

## Key Findings

- **Movies outnumber TV shows:** 6,126 movies vs 2,664 TV shows (about 70% / 30%).
- **Catalog growth:** titles added per year grew from 24 in 2014 to a peak of 2,016 in 2019. 2021 is lower (1,498) because the data stops partway through that year.
- **Top countries:** United States (3,681 titles), India (1,046), United Kingdom (805) and Canada (445). Co-productions count once for each country involved. "Unknown" ranks 3rd (829 titles), but it only labels missing country data, not a real country.
- **Most common genres:** International Movies (2,752), Dramas (2,426) and Comedies (1,674).
- **Ratings over time:** TV-MA is the most common content rating. It made up roughly a third of titles added each year from 2016 onward (33-39%), so it grew with the catalog rather than faster than it.
- **IMDb ratings by genre:** among genres with 100+ rated titles, Documentaries (6.88, n=357) and Stand-Up Comedy (6.73, n=189) rate highest, and Horror Movies (5.30, n=162) lowest. Genres with only 1-3 rated titles (such as TV Thrillers or Science & Nature TV) top the raw list but rest on almost no data, so they are not meaningful.

## Limitations

- IMDb covers only part of the catalog: 6,285 of 8,790 titles have no rating, including 3,677 of 6,126 movies.
- The IMDb file contains only movies, so the ~56 TV shows that received a rating matched a movie's title by coincidence. They are kept as they are, so TV-genre averages should be treated with caution.
- The merge matches on title only, so two different films sharing a title would get the same rating.
- "Unknown" countries are counted as a category in the country ranking (see Key Findings).

## Tools Used

`Python`, `Pandas`, `NumPy`, `Kaggle Notebooks`

## How to Run It

1. Open a new Kaggle notebook and add both datasets listed above as inputs.
2. Upload or copy in the notebook from this repo. The file paths in it are Kaggle-specific (`/kaggle/input/...`).
3. Run the cells from top to bottom. Running a cell twice can cause errors (for example, `date_added` is already converted to a date after the first run), so use Restart & Run All if something breaks.
