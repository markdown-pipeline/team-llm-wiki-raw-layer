---
source: notion
source_id: "5f2c9a1e-0000-4000-8000-000000000001"
source_url: https://www.notion.so/acme/Payments-on-call-runbook-5f2c9a1e
space: acme
title: "Payments on-call runbook"
author: dana.cohen
created: 2026-01-12T07:45:00Z
updated: 2026-09-15T11:02:00Z
synced: 2026-10-06T06:00:05Z
content_hash: sha256:EXAMPLE-RUNBOOK
status: active
labels: [runbook, on-call]
---

# Payments on-call runbook

## Duplicate charge reports
1. Find the payment IDs in the support ticket.
2. Check whether the requests carried the same `Idempotency-Key` (logs: `payments-api`, field `idem_key`).
3. If keys differ, it's a client bug. Escalate to the SDK team.
4. If the keys match but two charges exist, page the payments lead (possible Redis outage).
