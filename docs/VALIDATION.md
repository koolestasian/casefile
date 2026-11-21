# Validation

Casefile uses deterministic synthetic generation, planted ground truth,
brute-force references and failure-path tests. The checks cover the engine,
application boundaries and review workflow.

## Run the checks

```sh
cargo build --locked
cargo test --locked
cargo clippy --locked --all-targets -- -D warnings
node ui/checks.cjs
```

The Rust tests run without a model call. The UI regression script uses Node's
standard library and a small DOM harness.

## Exactness and domain rules

| Check | Coverage |
|---|---|
| Fast clustering versus brute-force reference | Same proposed components on fuzzed inputs |
| Bounded top-k versus full reference search | Same scores, ordering and tie handling |
| Indexed pawn matching versus brute force | Same match outcomes, including conflicting serials |
| Series verification | Travel feasibility, clearance conflicts and supporting case evidence |
| Citation checks | Case/field resolution and invalid references |
| Audit integrity | Corruption detection and rejected writes against broken chains |
| Review state | Failed saves, stale responses and guarded approval paths |

## Synthetic evaluation

The committed dataset contains 2,000 cases, 1,500 baseline pawn records, 40 planted
series and 10 hard-constraint decoys. Curated demo leads are tagged separately and
excluded from the evaluation counts.

```sh
cargo run --locked -- eval --out evals/report.md
```

The [evaluation report](../evals/report.md) records series precision/recall,
property-match outcomes, extraction accuracy and matched-case comparisons. These
measurements describe the planted synthetic workload. The demo queue is designed
to exercise the review paths.

## Performance and reproducibility

```sh
cargo run --release --locked -- bench --scales 1 --reps 3
```

Each benchmark compares the optimized output with its reference before reporting
timings. The [benchmark report](../evals/bench.md) includes the workload size,
candidate funnel and timing repetitions.

Published replay fixtures retain case-tool interactions and final structured
outputs. Operator hooks, session metadata and local environment output are
excluded. Agent prompts are maintained separately.
