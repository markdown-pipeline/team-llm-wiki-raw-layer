# Sync checklist: keeping a raw layer current

Use it to review a home-made sync script, or to evaluate any tool.

## Changes
- [ ] Uses each source's own "changed since" mechanism: JQL `updated >=`, Confluence modified date, Notion last-edited, Drive changes feed, GitHub `since=`.
- [ ] Compares a **content hash** of the converted Markdown before rewriting a file, so there is no noisy diff when only metadata changed.
- [ ] Keeps a **cursor per source** in `knowledge/_state/` and commits it with the data.

## Renames and moves
- [ ] File names are based on the **stable source ID** (`<id>-<slug>.md`), so a rename doesn't create a duplicate.
- [ ] A moved page updates its path and leaves no orphan behind.

## Deletions
- [ ] Runs a **deletion sweep**: list everything at the source, compare with the manifest, and handle what's missing.
- [ ] Treats only a real "not found" as a deletion; timeouts and 5xx mean "try later".
- [ ] Follows a clear policy: remove the file, or mark it `status: deleted-at-source`.

## Safety
- [ ] **Big-change guard:** if a run would change or delete more than ~40% of a source, stop and ask a human.
- [ ] Read-only tokens with minimum scopes.
- [ ] Mirrors only what the repo's audience is allowed to read.
- [ ] Never writes into `knowledge/wiki/` (that layer belongs to your agents).

## Fidelity
- [ ] Tables, code blocks and links survive conversion.
- [ ] Attachments are stored under `knowledge/raw/_attachments/` and linked.
- [ ] Jira: comments and issue links are included, not only the description.

## Operations
- [ ] Runs on a schedule, and you notice when it stops (a silent sync is worse than none).
- [ ] Every run is a reviewable Git diff.
