---
mode: agent
---

# Crawl4AI → Supabase E2E Integration & Validation (Phase 4)

> Integrate the **Crawl4AI** crawler with our Supabase Edge Functions, validate the full ingestion → enrichment → search/chat pipeline, and produce auditable evidence. Work inside this repo: **/workspaces/Supabase-Services-Hooks-Guide/supa-crawl-chat** (cloned from https://github.com/bigsk1/supa-crawl-chat).

## Use ALL MCPs *each* step
- **sequentialthinking** — plan the steps and update the plan after each milestone
- **supabase** — run SQL, validate schema, inspect rows, and verify RLS behavior
- **brave-search** — open official docs (Supabase & Crawl4AI) for any command/API you invoke
- **filesystem** — write reports/evidence into `/workspaces/Supabase-Services-Hooks-Guide/reports`
- **memory** — track current state, learned pitfalls, and TODOs

## References (consult via brave-search)
- **Crawl4AI (official):** https://docs.crawl4ai.com/
- **Supabase – AI Models in Functions:** https://supabase.com/docs/guides/functions/ai-models
- **Supabase – Edge Functions Overview:** https://supabase.com/docs/guides/functions
- **Supabase – Serve locally:** https://supabase.com/docs/guides/functions/serve
- **Supabase – Deploy functions:** https://supabase.com/docs/guides/functions/deploy
- **Supabase – Manage secrets:** https://supabase.com/docs/guides/functions/secrets
- **Supabase – CLI reference:** https://supabase.com/docs/reference/cli

## Task (what to achieve)
Wire **Crawl4AI** so that real crawl results flow into our **`webhook-handler`** Edge Function, which triggers **`content-enrichment`** (summary + embeddings). Confirm successful writes to the database and that the data becomes searchable/chat‑ready.

## Constraints & guardrails
- **No secrets in source control.** Use environment variables and `supabase secrets`.
- Use `$SUPABASE_URL` and `$SUPABASE_ANON_KEY` for public function calls; keep the **service role key** server‑side only.
- If `ai-chat-completion` is tested, **omit `sessionId`** to allow auto‑creation of sessions.
- Produce **repeatable** commands and **short** logs (JSON snippets) as evidence.
- Keep reports under `/reports` and include timestamps in filenames.

## Step‑by‑step plan (execute with MCPs)
1) **Pre‑flight**
   - Confirm Supabase project is reachable: `npx supabase --help`, `supabase start` (if local).
   - Ensure secrets are present: `supabase secrets list` (we expect a valid `OPENAI_API_KEY`).
   - Record results to `/reports/phase4_preflight.md`.

2) **Start Crawl4AI stack**
   - Use repo’s compose file:  
     `docker-compose -f docker/crawl4ai-docker-compose.yml up -d`
   - Verify container health following **Crawl4AI** docs (API reachable, version shown).
   - Save service status to `/reports/phase4_crawl4ai_status.md`.

3) **Crawl a test URL**
   - Using the Crawl4AI client/API (per **docs.crawl4ai.com**), fetch one or more pages (e.g., docs or a simple article).
   - Capture for each page: `url`, `title`, `content`, `status_code`, plus `{ "source": "crawl4ai" }` metadata.
   - Store raw crawl results as `reports/phase4_crawl_results.json`.

4) **POST results → Supabase webhook**
   - For each crawled page, `POST` to:
     ```
     POST $SUPABASE_URL/functions/v1/webhook-handler
     Authorization: Bearer $SUPABASE_ANON_KEY
     Content-Type: application/json
     {
       "event": "page_processed",
       "data": {
         "site_id": "phase4-demo",
         "url": "...",
         "title": "...",
         "content": "...",
         "status_code": 200,
         "metadata": { "source": "crawl4ai" }
       }
     }
     ```
   - Log responses (HTTP code + JSON) to `/reports/phase4_webhook_calls.jsonl`.

5) **Validate enrichment & embeddings**
   - Using **supabase** MCP, check that:
     - A **page** row exists for each URL.
     - **content_enrichments** has a **summary** for each page.
     - **embeddings** (vector column/row) exist or are queued and then written.
   - If needed, call `content-enrichment` directly:
     ```
     POST $SUPABASE_URL/functions/v1/content-enrichment
     Authorization: Bearer $SUPABASE_ANON_KEY
     Content-Type: application/json
     { "pageId": "<uuid>", "enrichmentTypes": ["embeddings","summary"] }
     ```
   - Save query outputs to `/reports/phase4_db_checks.md` (short table snippets).

6) **Optional smoke on chat**
   - Call `ai-chat-completion` without `sessionId` and prompt it to reference the new content.
   - Save the response to `/reports/phase4_chat_smoke.json`.

7) **Real‑time & API**
   - Confirm a minimal real‑time subscription reflects insert/enrichment events (if the frontend is available).
   - Note any required env or client wiring in `/reports/phase4_realtime.md`.

8) **Evidence & summary**
   - Produce a **one‑page** status: `/reports/phase4_e2e_summary.md` including:
     - What passed/failed and short evidence (log snippets/row counts).
     - Timing (crawl → webhook → enrichment completion).
     - Any errors with root cause + fix.
     - Checkboxes for acceptance criteria (see below).
   - Also emit a machine‑readable `/reports/phase4_evidence.json` with:
     - `pages_ingested[]`
     - `enrichments[]`
     - `embeddings[]`
     - `function_calls[]` (name, latency_ms, status)

## Success criteria (must meet all)
- ✅ **Crawl4AI running** and returns page data (proof: service status + sample JSON).
- ✅ **Webhook accepted** each page (2xx + response JSON).
- ✅ **DB rows present** for: pages + content_enrichments (+ embeddings vectors).
- ✅ **Enrichment completed** (summaries present; embeddings stored or verifiably created).
- ✅ **Reproducible runbook** and evidence files exist under `/reports`.
- ✅ (Optional) **Chat/semantic search** can reference new content.

## What to deliver (filesystem writes)
- `reports/phase4_preflight.md`
- `reports/phase4_crawl4ai_status.md`
- `reports/phase4_crawl_results.json`
- `reports/phase4_webhook_calls.jsonl`
- `reports/phase4_db_checks.md`
- `reports/phase4_chat_smoke.json` (optional)
- `reports/phase4_realtime.md` (optional)
- `reports/phase4_e2e_summary.md`
- `reports/phase4_evidence.json`

## Known tips
- If a chat call fails on a missing `sessionId`, retry **without** it so the function creates one.
- Keep payloads **small** during testing; large pages can slow enrichment.
- Never commit secrets; prefer `supabase secrets set` for function env.

