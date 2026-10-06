# The Raw Layer for Your Team's LLM Wiki

*How to keep your team's Confluence, Notion, Google Docs, Jira and GitHub knowledge as
current Markdown files that your agents (Claude Code, Codex, Cursor, custom agents) and your
LLM wiki can work on.*

> Version 1.0 (2026-10). A practical procedure, tool-agnostic. You can follow it
> with exports and scripts today.

---

## 1. Why a raw layer?

The LLM-wiki pattern (Karpathy, April 2026) has three layers:

1. **Raw sources**: immutable originals.
2. **The wiki**: pages your agents compile and maintain.
3. **The schema**: `CLAUDE.md` / `AGENTS.md`, the conventions your agents follow.

Most write-ups focus on layer 2. For a **team**, layer 1 is where things break:
- the knowledge lives in many tools, not in a folder one person curates;
- exports go stale the day after you run them;
- deleted or moved pages linger and get cited;
- attachments, tables and threads get lost on conversion;
- nobody can tell where a "fact" came from.

A raw layer fixes this. It is a **faithful, current, Markdown mirror of your team's tools,
with provenance on every file**. Your agents digest *it*, not the live APIs.

## 2. The folder layout

```
knowledge/
├── raw/                      # machine-written, never hand-edited
│   ├── confluence/<space>/<page-id>-<slug>.md
│   ├── jira/<project>/<issue-key>.md
│   ├── notion/<workspace>/<page-id>-<slug>.md
│   ├── gdrive/<shared-drive>/<file-id>-<slug>.md
│   ├── github/<repo>/issues/<number>.md
│   └── _attachments/<source>/<id>/<filename>
├── wiki/                     # your agents' compiled pages (LLM-wiki layer)
├── _state/                   # sync cursors and manifests (not for agents)
│   └── <source>.manifest.json
└── CLAUDE.md / AGENTS.md     # conventions for your agents
```

Rules:
- **One file per source object** (page, issue, doc). Stable filename = `<source-id>-<slug>`,
  so renames don't create duplicates.
- **`raw/` is read-only for agents.** Agents write only to `wiki/`.
- Keep everything in **Git**, so every sync is a reviewable diff.

## 3. Frontmatter: provenance on every file

```yaml
---
source: confluence            # system of record
source_id: "123456789"        # stable ID in that system
source_url: https://acme.atlassian.net/wiki/spaces/ENG/pages/123456789
space: ENG                    # container (space / project / drive / repo)
title: "Payments service — ADR 014: idempotency keys"
author: jane.doe
created: 2026-03-02T10:14:00Z
updated: 2026-09-28T16:40:12Z # source-side last edit
synced: 2026-10-01T06:00:03Z  # when this file was written
content_hash: sha256:9f2c…    # hash of the converted body (see §5)
status: active                # active | deleted-at-source | moved
labels: [adr, payments]
links_out: [confluence:987654321, jira:PAY-412]
attachments: [_attachments/confluence/123456789/sequence.png]
---
```

Why it matters: your agents can cite `source_url`, filter by `space` or `labels`, and detect
staleness from `updated` and `synced`.

## 4. Per-source export notes (what to watch)
| Source | Change detection | Gotchas |
|---|---|---|
| Confluence | Query pages modified since the last cursor | Attachments and macros often break in conversion; keep attachments in `_attachments/`; columns/layouts flatten |
| Jira | JQL `updated >= <cursor>` | Comments and links matter more than the description; include them |
| Notion | Last-edited time per page/block | Nested pages and databases; exports add index/duplicate files you'll need to de-duplicate |
| Google Drive / Docs | Drive changes feed | Docs → Markdown loses some formatting; Sheets usually need CSV, not Markdown |
| GitHub issues/PRs | `since=` on the issues API | Separate issues from PRs; include review threads selectively |
| Slack | **Check the terms first.** Slack's API terms (Oct 2025) restrict third-party apps from keeping persistent copies or indexes of workspace data, and rate-limit non-Marketplace apps heavily. | Internal, customer-built apps are treated differently. Get your own legal reading before mirroring Slack |
| Microsoft 365 (SharePoint, OneDrive, Teams) | Graph delta queries | Microsoft's API terms allow local copies for the app's intended use, but require keeping them current, **including deletions** |

## 5. The sync checklist (where most DIY pipelines fail)

Learned from teams that run incremental sync in production:

1. **Every source has a different idea of "changed".** Use each source's own delta or
   modified-since API. There is no generic connector.
2. **Metadata lies.** "Last modified" can change while the content didn't. Compare a
   **content hash** of the converted Markdown before rewriting a file (otherwise every run
   is a full re-sync and a noisy Git diff).
3. **Deletions are the hard part.** Most APIs won't tell you what was deleted. Periodically
   list everything at the source, compare with your manifest, and mark missing items
   `status: deleted-at-source` (or remove them, according to your policy). Run it less
   often than edit sync, but run it.
4. **Big changes go to a human first.** If a run would change or delete more than ~40% of a
   source's files, stop and ask. A site redesign, a permission change or a bad token can
   look like "everything was deleted".
5. **A 404 means gone; a 5xx or timeout means "try later".** Only the first counts as a deletion.
6. **Only pay for what changed.** Cache expensive conversions (PDF → text, image
   descriptions) by content hash.
7. **Keep the cursor per source** in `_state/` and commit it with the data, so a sync is
   resumable and auditable.

## 6. Tell your agents how to use it (CLAUDE.md / AGENTS.md snippet)

```markdown
## Company knowledge
- `knowledge/raw/` mirrors our tools (Confluence, Jira, Notion, Drive, GitHub). It is
  read-only. Never edit files there.
- Before answering questions about specs, decisions or tickets, search `knowledge/raw/`
  (and `knowledge/wiki/` if present).
- Cite the `source_url` from the file's frontmatter for any fact you use.
- Ignore files with `status: deleted-at-source`.
- Prefer the most recently `updated` file when sources conflict, and mention the conflict.
```

## 7. Permissions and safety (team version)
- Mirror only what the **intended audience** of the repo may read. Separate repos or folders per audience are simpler than per-file ACLs.
- Use read-only API tokens with minimum scopes.
- Keep the mirror inside your environment (your Git host or storage).
- Have a policy for removing data when the source is deleted or access is revoked.

## 8. A minimal weekly routine
- [ ] Incremental sync ran on schedule (check cursor timestamps).
- [ ] The deletion sweep ran (at least daily for active sources).
- [ ] No sync was blocked by the big-change guard (or it was reviewed).
- [ ] The agents' `wiki/` pages that cite changed raw files were refreshed.

---

*Prefer not to maintain the pipeline yourself? We're building **Markdown Pipeline** (private early access; Jira and local folders today). [Ask for early access](https://github.com/markdown-pipeline/team-llm-wiki-raw-layer/discussions/new?category=early-access) or [vote for the next source](https://github.com/markdown-pipeline/team-llm-wiki-raw-layer/discussions/1).*
