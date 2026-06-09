---
name: citedy-warm-lead
title: "Warm Lead Reports"
description: >
  Turn one company domain into a warm account report with recent signals,
  evidence URLs/snippets, buyer-role candidates, scoring, review policy, and
  grounded outreach drafts. Export JSON, CSV, or Excel .xlsx. Powered by Citedy.
version: "1.0.0"
author: Citedy
tags:
  - sales-intelligence
  - warm-leads
  - outbound
  - account-research
  - agents
metadata:
  openclaw:
    requires:
      env:
        - CITEDY_API_KEY
    primaryEnv: CITEDY_API_KEY
  compatible_with: "citedy-seo-agent@3.7.0"
privacy_policy_url: https://www.citedy.com/privacy
security_notes: |
  API keys (prefixed citedy_agent_) are stored in the user's local agent
  configuration. Keys authenticate only against Citedy API endpoints
  (www.citedy.com/api/agent/*). All traffic is TLS-encrypted.
---

# Citedy Warm Lead — Skill Instructions

You are now connected to **Citedy Warm Lead**. Base URL:
`https://www.citedy.com`.

Use this skill to turn one company domain into a warm account report before
outreach. The report answers: who should we contact, why now, and what should a
grounded first message say?

## When to Use

Activate this skill when the user asks to:

- Research a target account before outbound
- Find a reason to contact a company now
- Identify likely buyer roles for one company domain
- Generate evidence-grounded outreach drafts
- Export a warm lead report as JSON, CSV, or Excel
- Create a client-ready account brief for an SDR, founder, or agency workflow

Do not use this skill as a generic email finder. V1 does not send email
automatically.

## Setup

If `CITEDY_API_KEY` is not configured, help the user get one.

### Option A — Existing Dashboard Key

1. Sign in at `https://www.citedy.com`.
2. Open dashboard settings for agent/API keys.
3. Copy a key that starts with `citedy_agent_`.
4. Store it as `CITEDY_API_KEY`.

### Option B — Register This Agent

Preferred: run the included registration script:

```bash
node scripts/register.mjs [agent_name]
```

The script prints an approval URL. If `agent_name` is omitted, it defaults to
`agent-<hostname>`.

Alternative: call the API directly:

```http
POST https://www.citedy.com/api/agent/register
Content-Type: application/json

{"agent_name": "warm-lead-agent"}
```

Ask the user to open `approval_url`, approve the agent, then store the issued
`citedy_agent_...` key.

## Core Workflow

### 1. Start Report

```http
POST /api/agent/citedy-warm-lead
Authorization: Bearer <CITEDY_API_KEY>
Content-Type: application/json

{
  "domain": "acme-saas.example.com",
  "icp": "content-led-saas",
  "maxContacts": 3,
  "drafts": 3,
  "freshnessDays": 90
}
```

- Required: `domain`.
- Optional: `icp`, `buyerRoles`, `maxContacts`, `drafts`, `freshnessDays`,
  `idempotencyKey`.
- Cost: 40 Citedy credits for accepted new runs.
- Idempotent retry: returns existing run with `credits_charged: 0`.
- Response: `{ run: { id, status }, credits_charged, idempotent? }`.

### 2. Poll Until Done

```http
GET /api/agent/citedy-warm-lead/{runId}
Authorization: Bearer <CITEDY_API_KEY>
```

Poll until `status` is `completed`, `blocked`, or `failed`.

Completed results include:

- input domain and normalized company identity
- recent account signals
- bounded evidence URLs/snippets
- up to 3 buyer candidates with rationale and confidence
- score/confidence fields
- policy/safety output
- up to 3 evidence-grounded outreach drafts
- safe cost/debug counters
- flattened export rows

### 3. Export

```http
GET /api/agent/citedy-warm-lead/{runId}/export?format=xlsx
Authorization: Bearer <CITEDY_API_KEY>
```

Supported formats:

- `json` — canonical agent result object
- `csv` — spreadsheet rows
- `xlsx` — client/SDR-friendly Excel report

## User-Facing Response Pattern

Summarize the report in business language:

```text
Warm lead report complete for acme-saas.example.com.

Why now:
- Product launch signal from the changelog
- Hiring signal around growth
- Recent content around AI search visibility

Best first contacts:
- Growth leader — 82 confidence
- Founder — 78 confidence
- Content lead — 74 confidence

Drafts:
- 3 evidence-grounded first-message options
- Review required before sending

Export:
- JSON, CSV, or Excel .xlsx
```

## Guardrails

- Never claim Citedy sends email automatically.
- Never invent people, emails, launches, or metrics not present in evidence.
- Keep outreach drafts tied to evidence IDs or source URLs.
- If the run is blocked or low-confidence, say so clearly and recommend manual
  review.
- One run is one company domain.
- Default ICP is `content-led-saas` unless the user specifies otherwise.

## Quick Reference

| Endpoint                                     | Method | Cost       |
| -------------------------------------------- | ------ | ---------- |
| `/api/agent/citedy-warm-lead`                | POST   | 40 credits |
| `/api/agent/citedy-warm-lead/{runId}`        | GET    | free       |
| `/api/agent/citedy-warm-lead/{runId}/export` | GET    | free       |
| `/api/agent/me`                              | GET    | free       |
| `/api/agent/health`                          | GET    | free       |
