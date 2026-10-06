---
source: confluence
source_id: "123456789"
source_url: https://acme.atlassian.net/wiki/spaces/ENG/pages/123456789
space: ENG
title: "ADR 014: Idempotency keys for the payments API"
author: jane.doe
created: 2026-03-02T10:14:00Z
updated: 2026-09-28T16:40:12Z
synced: 2026-10-06T06:00:03Z
content_hash: sha256:EXAMPLE-ADR014
status: active
labels: [adr, payments]
links_out: [jira:PAY-412]
attachments: []
---

# ADR 014: Idempotency keys for the payments API

**Status:** Accepted · **Date:** 2026-03-02 · **Deciders:** payments team

## Context
Mobile clients retry on timeout. Without idempotency, retries can create duplicate charges.

## Decision
- Clients send an `Idempotency-Key` header (UUID v4) on `POST /payments`.
- The server stores key → response for **24 hours** (Redis).
- Same key with a different body returns `409 Conflict`.

## Consequences
- Requires Redis availability for payment creation.
- SDKs must reuse the key on retry.
