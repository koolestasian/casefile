# Architecture

Casefile separates proposal generation, deterministic verification and human
decisions. Language models structure narratives and propose next steps. The Rust
engine evaluates links, property records and citations. The console presents the
evidence and records the reviewer's decision.

## Two crates, explicit boundaries

| Layer | Responsibility | Implementation |
|---|---|---|
| Domain core | Types, case linkage, verification, solvability, property rules, citations and scenario economics | [`casefile-core`](../crates/core/src/lib.rs) |
| Application | Data loading, synthetic generation, MCP tools, HTTP server, replay, evaluation and audit persistence | [`casefile`](../crates/casefile/src/main.rs) |
| Interface | Case queue, reports, property leads, series review, action approval and algorithm exploration | [`ui/`](../ui/README.md) |

The domain core performs no I/O. Its inputs and outputs are ordinary Rust values,
which makes the rules independently testable and reusable through MCP and HTTP.
The application owns files, processes, network transport and persistence.

## From report to reviewed lead

1. **Load the case.** The store reads the synthetic dataset and overlays available
   structured extraction outputs from recorded runs.
2. **Generate candidates.** Linkage scores combine behavior, distance and time.
   Property lookup gathers records sharing a category, serial or distinctive mark.
3. **Verify the proposal.** Series checks evaluate travel feasibility, clearance
   conflicts and supporting case facts. Property rules distinguish exact serial
   leads, markings, similar descriptions and conflicting serials.
4. **Present the evidence.** The console keeps the source report, engine results,
   citations and proposed steps together.
5. **Record the decision.** Accept/reject actions carry a reason and are appended
   to the hash-chained audit log.

## Agent-tool boundary

The [MCP server](../crates/casefile/src/mcp.rs) exposes case retrieval, similarity,
series proposals, verification, solvability, pawn lookup, citation checks and
approval requests as structured tools. Agent definitions declare their allowed
tools and output schemas.

The public demo replays recorded outputs and tool activity. This preserves the
case-review flow without live inference or a model credential. Product-agent
prompts remain outside the public checkout.

## Evidence and decisions

Citation tokens identify a case and field, such as `[CASE_ID:field]`. The
[citation module](../crates/core/src/citations.rs) resolves them against the data.
An exact serial match is presented as a property lead; similar descriptions and
markings retain their own review categories.

The [audit module](../crates/casefile/src/audit.rs) links each decision to its
predecessor with SHA-256. Verification identifies the first broken link. The
application rejects writes when the existing chain is corrupt.

## Reproducibility and deployment

- The generator uses a fixed seed and synthetic ground truth for repeatable tests.
- Recorded agent outputs supply the extraction and proposal layers of the demo.
- The same core powers the console, MCP tools, evaluation harness and benchmarks.
- The Docker image contains the UI, synthetic case data and recordings. Evaluation
  ground truth is excluded from that image.
- UI assets are vendored for offline use; [vendor notices](../ui/vendor/LICENSES.txt)
  accompany the distributed libraries.

See [algorithms](ALGORITHMS.md) for the exact fast paths and
[validation](VALIDATION.md) for the checks that exercise these boundaries.
