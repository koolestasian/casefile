# Agent interfaces

The public checkout contains agent definitions, tool allowlists and output schemas.
The offline demo uses recorded runs in `data/recordings/`.

| Agent | Role |
|---|---|
| Extractor | Structure the report narrative |
| Triage | Propose a disposition and next steps with citations |
| Series hunter | Propose linked-case hypotheses through engine tools |
| Skeptic | Challenge those hypotheses through verification |
| Packet writer | Assemble a cited lead packet |
| Victim liaison | Draft an update for approval |
| LLM-only baseline | Supply a tool-free comparison on fixed evaluation subsets |

`agent.json` declares the model, input type and allowed MCP tools. `schema.json`
defines the structured output. Live recording uses separately maintained agent
prompts; the public demo runs from the checked-in recordings.
