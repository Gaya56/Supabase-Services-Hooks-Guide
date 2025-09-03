---

applyTo: '\*\*/\*.ts'
---

# Supa‑Crawl‑Chat × Crawl4AI — Integration & Validation Instructions (Phase 4)

**Agent kickoff prompt (paste & run):**
“Use **all MCPs** to integrate **Crawl4AI** with our Supabase Edge Functions. Start the Crawl4AI service, crawl a test URL, POST the result to our `webhook-handler`, confirm enrichment (summary + embeddings) is created, and deliver a one‑page status (evidence logs + DB checks + pass/fail + next steps). Use the references in this doc.”

---

## Use ALL MCPs each step

* **sequentialthinking** (plan)
* **supabase** (queries, schema validation, inserts)
* **brave-search** (verify in official Supabase docs)
* **filesystem** (read/write reports)
* **memory** (track lessons + state)

---

## Pre‑flight (Supabase)

Open and read (via **brave-search**) the official Supabase docs:

* AI Models in Functions: [https://supabase.com/docs/guides/functions/ai-models](https://supabase.com/docs/guides/functions/ai-models)
* Edge Functions Overview: [https://supabase.com/docs/guides/functions](https://supabase.com/docs/guides/functions)
* Serve locally: [https://supabase.com/docs/guides/functions/serve](https://supabase.com/docs/guides/functions/serve)
* Deploy functions: [https://supabase.com/docs/guides/functions/deploy](https://supabase.com/docs/guides/functions/deploy)
* Manage secrets: [https://supabase.com/docs/guides/functions/secrets](https://supabase.com/docs/guides/functions/secrets)
* CLI reference (functions): [https://supabase.com/docs/reference/cli](https://supabase.com/docs/reference/cli)

Confirm local stack: `npx supabase --help`, `supabase start`.

---

## Pre‑flight (Crawl4AI)

Review the **official Crawl4AI docs** to align on install, Docker usage, and client APIs:
**Docs:** [https://docs.crawl4ai.com/](https://docs.crawl4ai.com/)

> Use the Quickstart / Installation / Docker / API sections in the docs above to confirm your local setup, service endpoints, and client usage patterns before integration.

---

## Context: Where we are

* **Edge Functions + AI are working** (chat completion, content enrichment, webhook) and the **OpenAI key is valid**; `ai-chat-completion` works when it creates a new session, `content-enrichment` returns a summary, and `webhook-handler` triggers enrichment end‑to‑end.&#x20;
* We have a robust “processing backend” (schema + RLS + embeddings + enrichment) **ready to receive crawl results**; **Crawl4AI is not yet wired in**.&#x20;

---

## Phase 4 Goal

**Connect Crawl4AI → Supabase `webhook-handler` → enrichment → searchable content.**
Repo foundation: **`supa-crawl-chat`** (Dockerized; frontend + API + Supabase + crawler infra already scaffolded).&#x20;

---

## Step‑by‑step Integration

### 1) Configure environment

Set/confirm the following (no secrets in code; use Supabase Secrets / `.env`):

* `CRAWL4AI_API_TOKEN` (per Crawl4AI docs: [https://docs.crawl4ai.com/](https://docs.crawl4ai.com/))
* `SUPABASE_URL`, `SUPABASE_ANON_KEY` (client) and `SUPABASE_SERVICE_ROLE_KEY` (server‑side only)
* `OPENAI_API_KEY` (already validated)&#x20;

> Record configuration diffs via **filesystem** and pin them in **memory**.

---

### 2) Start the crawler stack

From the project root:

```bash
cd /workspaces/Supabase-Services-Hooks-Guide/supa-crawl-chat
docker-compose -f docker/crawl4ai-docker-compose.yml up -d
```

This brings up the Crawl4AI service alongside the rest of the stack so we can perform true end‑to‑end tests.&#x20;
(See Docker guidance in Crawl4AI docs: [https://docs.crawl4ai.com/](https://docs.crawl4ai.com/))

---

### 3) Wire Crawl4AI → Supabase webhook

**Target webhook (Edge Function):**
`POST  $SUPABASE_URL/functions/v1/webhook-handler` (bearer: `$SUPABASE_ANON_KEY` for public testing)

**Expected payload shape** (matches earlier tests):

```json
{
  "event": "page_processed",
  "data": {
    "site_id": "00cfd1dc-fb12-4745-90a0-066a8affd1f0",
    "url": "https://example.com/some-page",
    "title": "Page Title",
    "content": "Raw or cleaned page content...",
    "status_code": 200,
    "metadata": { "source": "crawl4ai" }
  }
}
```

**Example Python wrapper (run after a Crawl4AI fetch)**

```python
import os, requests, json

SUPABASE_URL = os.environ["SUPABASE_URL"]
SUPABASE_ANON_KEY = os.environ["SUPABASE_ANON_KEY"]
WEBHOOK = f"{SUPABASE_URL}/functions/v1/webhook-handler"

def post_to_webhook(page):
    payload = {
        "event": "page_processed",
        "data": {
            "site_id": page.get("site_id", "default-site"),
            "url": page["url"],
            "title": page.get("title") or page["url"],
            "content": page.get("content", ""),
            "status_code": page.get("status_code", 200),
            "metadata": {"source": "crawl4ai"}
        }
    }
    r = requests.post(
        WEBHOOK,
        headers={"Authorization": f"Bearer {SUPABASE_ANON_KEY}",
                 "Content-Type": "application/json"},
        data=json.dumps(payload),
        timeout=30
    )
    r.raise_for_status()
    return r.json()
```

> For how to retrieve page results with the Crawl4AI client/service, consult the official API usage patterns in **[https://docs.crawl4ai.com/](https://docs.crawl4ai.com/)** (client calls, callbacks, or result streams).

---

### 4) Validate enrichment + embeddings

After posting a page, trigger/confirm enrichment:

* The `webhook-handler` should enqueue/trigger `content-enrichment`. (Already validated in Phase 3 tests.)&#x20;
* Verify **summary** and **embeddings** exist for the page (DB checks via **supabase** MCP).
* Confirm function responses are clean JSON and no auth errors occur.&#x20;

**Quick verification (curl mirrors prior success):**

```bash
# Re-run enrichment by pageId if needed
curl -X POST "$SUPABASE_URL/functions/v1/content-enrichment" \
  -H "Authorization: Bearer $SUPABASE_ANON_KEY" -H "Content-Type: application/json" \
  -d '{"pageId":"<existing-page-uuid>", "enrichmentTypes":["embeddings","summary"]}'
```

Earlier runs returned successful summaries and indicated embeddings processing.&#x20;

---

### 5) Frontend (TS) readiness checks

*Update TypeScript code (applies to `**/*.ts`) to surface pipeline status:*

* Add typed clients for calling:

  * `ai-chat-completion`
  * `content-enrichment`
  * `webhook-handler`
* Surface **status toasts** or banners when a page is ingested → enriched → searchable.
* Gate UI actions on **real‑time** events (subscriptions) so users see progress live. (Real‑time patterns were previously validated.)&#x20;
* Ensure **env access** in TS is typed and safe (no secrets in client bundles).

> Keep a short build log via **filesystem**; capture UX deltas and errors in **memory**.

---

## Quality Gates & Acceptance

Mark **Phase 4** complete when:

1. Crawling a real URL produces a **page** row and, shortly after, **content\_enrichments** + **embeddings** for that page.&#x20;
2. The frontend can submit a URL, display ingestion status, and allow search/chat over enriched content.&#x20;
3. Real‑time updates reflect each stage (ingestion → enrichment → ready).&#x20;

---

## Runbook (minimal commands)

```bash
# Start crawler stack
cd /workspaces/Supabase-Services-Hooks-Guide/supa-crawl-chat
docker-compose -f docker/crawl4ai-docker-compose.yml up -d   # From repo

# Sanity: secrets present
supabase secrets list                                        # OpenAI key already validated

# Smoke tests (Edge Functions)
curl -X POST "$SUPABASE_URL/functions/v1/ai-chat-completion" \
  -H "Authorization: Bearer $SUPABASE_ANON_KEY" -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"Ping"}], "userId":"test-user","profile":"default"}'

curl -X POST "$SUPABASE_URL/functions/v1/webhook-handler" \
  -H "Authorization: Bearer $SUPABASE_ANON_KEY" -H "Content-Type: application/json" \
  -d '{"event":"page_processed","data":{"site_id":"demo","url":"https://example.com","title":"Demo","content":"Hello","status_code":200}}'
```

These mirror the previously successful validation path for chat, enrichment, and webhook processing.&#x20;

---

## References

* **Crawl4AI — Official Docs:** [https://docs.crawl4ai.com/](https://docs.crawl4ai.com/)  *(installation, Docker usage, client APIs, and examples)*
* **Supabase — AI & Edge Functions:** links in the Pre‑flight section above
* **Project evidence & status:** prior validations, commands, and status rollups confirming Phase 3 completion and \~85% overall progress. &#x20;

---

### Notes for reviewers

* This integration plan is tailored to the cloned repo **`bigsk1/supa-crawl-chat`** and its Docker‑based crawler scaffolding. The repo already includes the pieces needed to start Crawl4AI and connect outputs to our Supabase pipeline.&#x20;
* Keep logs concise; prefer structured JSON responses and **short** evidence snippets for the status report.
