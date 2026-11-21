# Engine benchmark (`casefile bench`)

Seed dataset tiled into k one-year copies. Median of 3 runs, release build, this machine; "same" = optimised output equals the brute-force reference exactly. Threshold 0.9.

| k | cases | pawn | step | reference ms | optimised ms | speed-up | same |
|---|---|---|---|---|---|---|---|
| 1 | 2000 | 1510 | propose_clusters | 217.4 | 13.5 | 16.1x | yes |
| 1 | 2000 | 1510 | all_hits (pawn matching) | 5173.2 | 95.0 | 54.4x | yes |
| 1 | 2000 | 1510 | similar_cases k=5 (50 probes) | 21.8 | 11.7 | 1.9x | yes |

Pair funnel on the seed dataset (k=1): how many pairs each stage of propose_clusters still has to look at.

| threshold | all pairs | in 180-day window | in 30 km box | pass upper bound | edges |
|---|---|---|---|---|---|
| 0.5 | 1999000 | 1462213 | 1462213 | 595120 | 6309 |
| 0.9 | 1999000 | 1462213 | 1462213 | 68751 | 761 |

Why it is faster, with no change to results:
- propose_clusters: time-sorted sweep (only pairs inside the 180-day window are visited), an exact score upper bound (full MO agreement) rejects pairs before MO work, MO features precomputed as a bitmask and interned signature ids (no per-pair allocation), work split across threads.
- all_hits: pawn records normalised once and indexed by category, serial and marking; an item is compared only with records that can match it under the rules.
- similar_cases: bounded min-heap top-k (O(n log k)) with the same upper bound used as a branch-and-bound cut against the current k-th best.
