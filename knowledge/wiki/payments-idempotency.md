# Payments: idempotency (compiled page, example)

*Compiled by an agent from the raw layer on 2026-10-06. Sources are listed at the end.*

## Current state
- `POST /payments` accepts an `Idempotency-Key` (UUID v4). Keys are kept for 24 h in Redis ([ADR 014](../raw/confluence/ENG/123456789-adr-014-idempotency-keys.md), [PAY-412](../raw/jira/PAY/PAY-412.md)).
- **Open issue:** retries with re-ordered JSON keys get a wrong `409`. A fix (canonical JSON hash) is in staging ([PAY-415](../raw/jira/PAY/PAY-415.md)). The SDK must also reuse the key on retry ([acme/payments#231](../raw/github/acme-payments/issues/231.md)).
- **On-call:** see the duplicate-charge procedure ([runbook](../raw/notion/acme/5f2c9a-payments-on-call-runbook.md)).

## History
- A Postgres-based store was explored and dropped. The spike issue was later deleted in Jira ([PAY-398](../raw/jira/PAY/PAY-398.md), `deleted-at-source`, not used as a current fact).

## Sources
ADR 014 (Confluence) · PAY-412, PAY-415 (Jira) · Payments on-call runbook (Notion) · acme/payments#231 (GitHub)
