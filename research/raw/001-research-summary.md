# Episode 001 research memo

## Status

**Partial research complete.** This memo is intentionally conservative: it uses public metadata and repository files only. It does not claim anything about the sound, lyrics, performance, or audience reception of the track beyond what the cited sources establish.

## Subject track

Spotify metadata identifies the subject as **「秘密の扉」 by 惑星通信社**. The Spotify track ID is `4QTGIIFqhOOdwJeKca7okQ`; the displayed release date is **2025-07-20** and the duration is **248 seconds (4:08)**. The direct source is [Spotify](https://open.spotify.com/track/4QTGIIFqhOOdwJeKca7okQ). The captured metadata is retained in `spotify-track-4QTGIIFqhOOdwJeKca7okQ.summary.json`; the raw HTML-derived capture has session values redacted.

## idol-playlist check

`bonsai/idol-playlist` contains a 15-song ranking snapshot dated around **2026-09-18 / 2026-09-19**. Its own KPI file explicitly says `sample_metrics=true` and that it is **not an observed ranking**. The ranking uses interest-oriented fields such as `ask_count`, `unique_askers`, `repeat_ask_count`, `recent_ask_count`, `lyrics_view`, and `youtube_view`; the score is described as a measure of being searched/listened to, not song quality.

The snapshot contains sample IDs such as `sample-003`, `sample-008`, and `sample-012`. A title, artist, or Spotify ID match for 「秘密の扉」／惑星通信社 was **not found** in the locally captured playlist snapshot. This is a catalog-coverage result, not evidence that the song is unpopular.

`idol-playlist` also contains a deterministic six-axis scorer in `app`/`api`-related code. The captured `idol-db` ranker uses weights M .25, C .15, U .20, L .20, O .10, R .10, but the subject track has no corresponding scored row in the captured data, so no score is fabricated.

## idol-db / idol-research check

`bonsai/idol-db` is the canonical-data layer. The captured README and files emphasize public-source ingestion, provenance, schemas, data, and API boundaries. The local data capture did not contain a direct record for the subject track. `bonsai/idol-research` defines a source-backed research loop: seed sources → collect → raw observation → normalize → entity resolution → relation/event graph → analysis → hypothesis → evaluation. It requires time fields such as `observed_at`, `published_at`, `accessed_at`, and `retrieved_at` where available.

## Working hypothesis for the episode

The first episode can ask: **「ランキングに載っていない曲を、私たちはどう聴き、どう記録し、どう語れるのか」**. The evidence supports a narrow argument: the current captured ranking dataset cannot be used to evaluate this track, and its own documentation warns against treating the sample score as a quality judgment. The stronger cultural interpretation—that absence from a ranking reflects a structural blind spot—remains a hypothesis requiring a larger, source-backed comparison.

## Next validation

1. Confirm the intended episode angle with the producer.
2. Obtain an authorized listening/performance context or creator-provided description before making sonic or lyrical claims.
3. Add a provenance-preserving record for the track to `idol-db` if it is in scope.
4. Compare at least three source types—Spotify metadata, official artist material, and an event or playlist observation—before generalizing.
5. Re-run the ranking pipeline after a real observation dataset exists; keep the current sample snapshot labeled as sample data.

## Captured raw files

- `spotify-track-4QTGIIFqhOOdwJeKca7okQ.summary.json`
- `spotify-track-4QTGIIFqhOOdwJeKca7okQ.metadata.txt` (session values redacted)
- `idol-playlist-KPI.yaml`
- `idol-playlist-data__rankings__2026-09-19.jsonl`
- `idol-db-api__music_ranker.py`
- `idol-research-research-plan.md`
- repository metadata, README, and file lists for `idol-db`, `idol-research`, and `idol-playlist`
