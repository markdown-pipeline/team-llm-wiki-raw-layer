# The raw layer for your team's LLM wiki

**Your team's knowledge lives in Jira, Confluence, Notion and Google Docs. Your agents need it as current Markdown files, with a link back to the source.**

This repo is a free, tool-agnostic template for that **raw layer**: the bottom layer of an [LLM wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) (Karpathy's pattern) that Claude Code, Codex, Cursor or your own agents can read.

```
your tools ──► knowledge/raw/   (one Markdown file per page / issue, with its source info)
                    │
                    ▼
               knowledge/wiki/  (pages your agents compile and keep)
                    │
                    ▼
               your agents answer questions, citing the source
```

## What you get
- **A folder layout** for many sources: `knowledge/raw/<source>/…`
- **A provenance header** for every file: source, ID, URL, updated, synced, status. Agents can cite it, and you can tell when a file is stale. See [GUIDE.md §3](GUIDE.md#3-frontmatter-provenance-on-every-file).
- **Agent rules**, ready to copy: [`CLAUDE.md`](CLAUDE.md) / [`AGENTS.md`](AGENTS.md).
- **A sync checklist** covering where home-made pipelines break: edits, **deletions**, renames, attachments. See [`checklists/sync-checklist.md`](checklists/sync-checklist.md).
- **Realistic samples** for a made-up company: [`knowledge/raw/`](knowledge/raw) and one compiled [`knowledge/wiki/`](knowledge/wiki) page.

## Quickstart (2 minutes)
1. Click **Use this template**, or copy `knowledge/`, `CLAUDE.md` and `AGENTS.md` into your repo.
2. Put your exports into `knowledge/raw/<source>/`, one file per page or issue, with the header from the samples.
3. Open Claude Code (or Codex / Cursor) in the repo and ask something only your docs know:
   *"What did we decide about idempotency keys, and which tickets implement it?"*
4. Your agent searches `knowledge/raw/`, answers, and cites the source URLs.

The full procedure is in **[GUIDE.md](GUIDE.md)**.

## Filling `raw/`: doing it yourself today
Free and existing tools that can produce the files (each has different strengths):

| Source | Options | Watch out for |
|---|---|---|
| Confluence | [confluence-markdown-exporter](https://github.com/Spenhouet/confluence-markdown-exporter) (CLI), Marketplace export apps | Macros, layouts and attachments; check how renames and deletions are handled |
| Notion | Notion's built-in Markdown export, [notion-to-md](https://github.com/souvikinator/notion-to-md) | Nested pages and databases; duplicate index files |
| Jira | [jira2md](https://pypi.org/project/jira2md/), Marketplace export apps | Comments and links matter as much as the description |
| Google Docs / Office files | Google Docs' Markdown download, [markitdown](https://github.com/microsoft/markitdown) | Sheets are better as CSV |

The hard part isn't the first export. It's **keeping it current**: edits, moved pages, and items deleted at the source that your agent keeps citing. [GUIDE.md §5](GUIDE.md#5-the-sync-checklist-where-most-diy-pipelines-fail) covers it.

## Or let Markdown Pipeline keep it current (private early access)
We're building **Markdown Pipeline**. It syncs your tools into this kind of raw layer, in your own folder or GitHub repo, and keeps it current every time you sync: edits and **deletions**, with source info on every file and a preview before anything is written.

- **It is not publicly available yet.** It works today for Jira Cloud and local folders, and we are opening it to a few teams first.
- 👉 **[Tell us your setup](https://github.com/markdown-pipeline/team-llm-wiki-raw-layer/discussions)** to be considered for early access.
- 👉 **[Vote for the next source](https://github.com/markdown-pipeline/team-llm-wiki-raw-layer/discussions)**. The votes decide what we build next.

Everything in this repo is free and works without it.

## Contribute
- Something missing or wrong for your source? [Open an issue](https://github.com/markdown-pipeline/team-llm-wiki-raw-layer/issues).
- Share how your team runs its raw layer in [Discussions](https://github.com/markdown-pipeline/team-llm-wiki-raw-layer/discussions).

## License
MIT. See [LICENSE](LICENSE).
