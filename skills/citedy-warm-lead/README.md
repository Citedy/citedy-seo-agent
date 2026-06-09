# citedy-warm-lead

Turn one company domain into a warm account report before outreach.

The report helps an agent answer:

- who should we contact?
- why now?
- what should the first message say?

## What It Does

`citedy-warm-lead` creates a downloadable account brief from one domain:

1. recent company/account signals
2. evidence URLs and snippets
3. up to 3 buyer-role candidates
4. scoring and policy output
5. up to 3 evidence-grounded outreach drafts
6. JSON, CSV, or Excel `.xlsx` export

It is not an email sender and not a generic email finder. It gives the context
before outreach.

## Install

### Claude Code, Codex, Droid, Antigravity, Cursor, OpenClaw, and more

```bash
npx skills add citedy/warm-lead
```

### Universal (openskills)

```bash
npx openskills install citedy/warm-lead
```

### Codex / local agent setup

```bash
git clone https://github.com/citedy/warm-lead.git ~/.agents/skills/citedy-warm-lead
```

## Get Your Agent API Key

This skill uses a Citedy Agent API key that looks like:

```text
citedy_agent_...
```

Set it as:

```bash
export CITEDY_API_KEY=citedy_agent_your_key_here
```

### Option A - Dashboard

1. Sign in at [citedy.com](https://www.citedy.com).
2. Open dashboard settings for agent/API keys.
3. Create or copy your Agent API key.
4. Export it as `CITEDY_API_KEY`.

### Option B - Register via API

```bash
curl -sS -X POST "https://www.citedy.com/api/agent/register" \
  -H "Content-Type: application/json" \
  -d '{"agent_name":"Warm Lead Agent"}'
```

Then:

1. open the returned `approval_url`
2. approve the agent
3. copy the issued `citedy_agent_...` key
4. export it as `CITEDY_API_KEY`

## Pricing

| Operation        | Credits | USD   |
| ---------------- | ------: | ----- |
| Warm lead report |      40 | $0.40 |
| Poll run status  |       0 | $0.00 |
| Export report    |       0 | $0.00 |

Notes:

- One run = one company domain.
- Existing idempotent retries return the previous run without a new charge.
- `1 credit = $0.01 USD`.

Top up at [citedy.com/dashboard/billing](https://www.citedy.com/dashboard/billing).

## Quick Start

Tell the agent the target account.

Examples:

- "Create a warm lead report for acme-saas.example.com"
- "Who should we contact at this SaaS company and why now?"
- "Give me a client-ready Excel report for this target account"
- "Generate first-message options grounded in recent company evidence"

## Execution Model

### Preferred Path

Use Citedy MCP tools when available:

- `citedy_warm_lead.start`
- `citedy_warm_lead.get`
- `citedy_warm_lead.export`

### REST Fallback

If MCP is unavailable, use the Agent API directly:

```bash
curl -sS -X POST "https://www.citedy.com/api/agent/citedy-warm-lead" \
  -H "Authorization: Bearer $CITEDY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"domain":"acme-saas.example.com"}'
```

Poll:

```bash
curl -sS "https://www.citedy.com/api/agent/citedy-warm-lead/$RUN_ID" \
  -H "Authorization: Bearer $CITEDY_API_KEY"
```

Export:

```bash
curl -sS "https://www.citedy.com/api/agent/citedy-warm-lead/$RUN_ID/export?format=xlsx" \
  -H "Authorization: Bearer $CITEDY_API_KEY" \
  -o warm-lead-report.xlsx
```

## Output

The completed report can include:

- normalized company identity
- recent account signals
- evidence URLs/snippets
- buyer candidates with role rationale and confidence
- account score and confidence bucket
- policy/safety output
- evidence-grounded outreach drafts
- export-ready rows for CSV/XLSX

## Guardrails

- V1 does not send email automatically.
- Review drafts before sending.
- Do not invent unsupported claims.
- If confidence is low or blocked, tell the user and recommend manual review.
