# Agent conventions for this knowledge repo

## Company knowledge
- `knowledge/raw/` mirrors our tools (Jira, Confluence, Notion, Google Drive, GitHub). It is **read-only**: never edit, move or delete files there.
- Before answering questions about specs, decisions, tickets or processes, search `knowledge/raw/`, and `knowledge/wiki/` if present.
- Cite the `source_url` from the file's front matter for every fact you use.
- Ignore files with `status: deleted-at-source`.
- When sources conflict, prefer the most recently `updated` file and mention the conflict.
- If `synced` is old (more than a few days), say that the information may be stale.
- Write synthesized pages **only** under `knowledge/wiki/`, and link back to the raw files you used.
