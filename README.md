[README.md](https://github.com/user-attachments/files/32843031/README.md)
# Content-Based Song Recommendation System

A song recommender built on a Spotify track dataset (about 4,600 tracks after cleaning). It supports two kinds of requests:

- **Free-text queries.** Ask for a genre, an artist or a mood, for example `"classic rock Fleetwood Mac"` or `"chill"`.
- **Seed-track queries.** Give a song and get similar songs back, for example `recommend_similar("Go Your Own Way")`.

The full analysis, code and outputs are in [`song_recommendation.ipynb`](song_recommendation.ipynb).

## How it works

Every track is described by three feature blocks: genres (TF-IDF), artist (TF-IDF) and nine Spotify audio features scaled to the 0 to 1 range. A request is scored against all tracks and the best matches are returned, with a small popularity boost as a tie-breaker and a cap on tracks per artist for variety.

Mood words such as `chill`, `party` or `sad` are translated into a target audio profile defined as percentiles of the dataset, so `"chill rock"` means rock songs that sound calm.

## Key decisions

| Problem found in the data | What was done |
|---|---|
| 26% of popularity values are 0, including famous songs | Zeros are treated as unknown and estimated from the artist median |
| Same song appears as remasters, edits and mono versions | Duplicates removed on a normalised title, keeping the most popular version |
| Release dates mix `2003` and `2003-05-01` formats | Parsed with `format="mixed"`, which avoids turning 680 valid dates into missing values |
| Missing genres | Left empty instead of filled with the most common genre |
| Track titles polluted text matching (`"songs by benny"` matched a title) | Titles are only a low-weight fallback, and filler words are removed |

## Evaluation

There is no listening history, so the seed-track recommender is compared with baselines on proxy metrics over 300 random seed tracks (10 recommendations each):

| Strategy | Genre overlap (higher is better) | Audio distance (lower is better) | Coverage |
|---|---|---|---|
| Random | 0.029 | 0.297 | 0.481 |
| Genre only | 0.639 | 0.260 | 0.325 |
| Audio only | 0.060 | 0.083 | 0.465 |
| Hybrid (default) | 0.615 | 0.196 | 0.413 |

The hybrid keeps most of the genre quality and moves closer to the seed in sound, but it stays much closer to genre only than to audio only. The notebook explains why and how to shift the weights.

## Limitations

- Purely content-based, with no user behaviour, so it cannot learn taste.
- Genres come from the artist, not the track, so tracks by one artist share the same genres.
- Weights and mood profiles are hand-tuned, not learned from feedback.
- The catalogue leans toward English-language pop and rock from the 1990s to the 2010s.
- The metrics describe similarity, not whether a listener would enjoy the result.

## Run it

```bash
pip install -r requirements.txt
jupyter notebook song_recommendation.ipynb
```

Place `song_recomendation_B.csv` in the same folder as the notebook, or change `DATA_PATH` in the first code cell.
