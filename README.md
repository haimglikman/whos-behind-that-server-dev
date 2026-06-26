### v1.17.2 — bug fix (server-dev) | Admin: v2.11.1 | Client: v1.13.0
- Fixed investigate/detect returning 500 — extractJSON now handles JSON arrays (Stage 1 returns an array of pair results, not a single object)

### v1.17.1 — bug fix (server) | Admin: v2.11.0 | Client: v1.13.0
- Stage 1 connection detection: switched from Sonnet to Haiku (~75% cheaper per token)
- Stage 1: batched 4 pairs per call instead of 1 (~75% fewer API calls)
- Stage 1: trimmed prompt from ~500 to ~180 input tokens per pair
- Stage 2: trimmed synthesis prompt (~50% fewer input tokens)
- Combined saving: ~85-90% token reduction for investigation vs v1.17.0

### v1.17.0 (server) | Admin: v2.11.0 | Client: v1.13.0
- New POST /investigate/detect — Stage 1: evaluates every post pair for meaningful connection, returns connection graph and clusters using union-find
- New POST /investigate/synthesize — Stage 2: generates cluster name and synopsis for a connected cluster
