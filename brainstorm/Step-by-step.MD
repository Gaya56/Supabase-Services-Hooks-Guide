# **1. Set up the base project (Supa‑Crawl‑Chat)**

1. Clone the repository and install dependencies. Run:

git clone https://github.com/bigsk1/supa-crawl-chat.git

cd supa-crawl-chat

pip install -r requirements.txt

cd frontend && npm install

1. The README shows that you need Python packages for the API and Node modules for the Next.js frontend .
2. Create and fill out the .env file. Copy .env.example to .env and add your API tokens and Supabase credentials. The project lists required variables such as CRAWL4AI_API_TOKEN, SUPABASE_URL, SUPABASE_DB, SUPABASE_KEY, SUPABASE_PASSWORD, and OPENAI_API_KEY . Only the Supabase and OpenAI credentials need to be supplied; everything else has defaults.
3. Run the stack locally (recommended via Docker). The README provides docker scripts that start Supabase (database, REST and Realtime), the API server, the crawler, and the frontend in one command . Alternatively, you can run python run_api.py for the backend and npm run dev for the frontend .
4. Verify that crawling and search work. Use the built‑in commands to crawl a domain and then test the search and chat endpoints (the README provides example curl commands). You can also open the included Streamlit explorer to run SQL queries on crawled data.

# **2. Prepare your Supabase environment**

1. Create a Supabase project and install the CLI. Supabase’s CLI documentation instructs you to authenticate (supabase login), link your local repository to the remote project (supabase link --project-ref <YOUR_PROJECT_REF>) and then push database changes or deploy functions . The CLI reference explains that linking is required before running supabase db push or db pull .
2. Enable required extensions. In the Supabase dashboard (Database → Extensions), enable pgvector for vector embeddings and pg_cron for scheduled jobs. Supabase’s scheduling guide explains that you can use pg_cron combined with pg_net to call Edge Functions on a schedule .
3. Set up row‑level security and policies. When you create new tables, enable row‑level security with ALTER TABLE <schema>.<table> ENABLE ROW LEVEL SECURITY . Without policies, the table is inaccessible to the public anon key . Later steps will add policies so each agent only accesses its own data.

# **3. Add Natural‑DB patterns**

1. Examine the Natural‑DB repository. This repo uses a dedicated memories schema and a restricted memories_role to ensure the LLM can only operate within its own schema . It also shows how to store different kinds of memory: message history, vector embeddings and structured SQL tables . It uses pg_cron and pg_net to schedule prompts and trigger Edge Functions .
2. Create your own integrations schema and role. Use the same pattern: define a schema (integrations) and create a restricted database role for your ingestion functions. This keeps scraped data separate from the rest of your database and limits what the agents can access.
3. Define normalized tables and relationships. Based on your data‑ingestion use case, create tables such as:
    - agents (id, name, webhook_secret).
    - sources (id, agent_id → agents.id, domain).
    - items (id, source_id → sources.id, url, title, content, vector embeddings).
    - enrichments (id, item_id → items.id, summary, sentiment, entities).
    Natural‑DB shows similar normalized models for runs, recipes and feedback . Add foreign keys to maintain relationships, and indexes for faster queries.
4. 
5. Implement row‑level policies. For example, create a policy that allows an agent to SELECT only rows where agent_id matches the authenticated user’s ID. Supabase’s RLS guide provides examples of row‑level policies that act like WHERE clauses .

# **4. Deploy and connect Edge Functions**

1. Write ingestion and orchestration functions. Use the Natural‑DB functions as patterns. For example, copy supabase/functions/telegram-input/index.ts and adjust it to accept webhook data from your scrapers; it validates the request, normalizes the payload and inserts rows into your tables. The natural-db function coordinates memory creation and scheduling. In both files, environment variables such as SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY and OPENAI_API_KEY are accessed via Deno.env.get .
2. Deploy the functions. Run supabase functions deploy scraper-input (or your chosen function names). If you want the endpoints to be publicly callable, add the --no-verify-jwt flag . The Environment Variables doc notes that the SUPABASE_URL and SUPABASE_SERVICE_ROLE_KEY are automatically injected into Edge Functions .
3. Set up webhooks. In the Supabase dashboard, create a new database webhook for each table/event you need. The Webhooks documentation explains that webhooks trigger on INSERT, UPDATE or DELETE and send data to an HTTP endpoint . Use this to call your ingestion function whenever a scraper adds new data. For outgoing webhooks (e.g., notifications), you can configure triggers to call external APIs via pg_net .
4. Configure scheduled jobs. Use pg_cron to call your orchestration function periodically—for example, to deduplicate or enrich scraped content. Supabase’s scheduling guide shows how to store secrets in the vault and schedule periodic HTTP requests .

# **5. Expose and consume the data**

1. Realtime subscriptions. If your frontend needs live updates when new items arrive, use Supabase Realtime. The Realtime guide explains that you can subscribe to INSERT/UPDATE events on tables via broadcasts or Postgres changes .
2. REST and SQL querying. Supabase automatically exposes a RESTful API at https://<project_ref>.supabase.co/rest/v1/. You can query your normalized tables directly via HTTP or via the Supabase client library. This API works with RLS and scales automatically.
3. Test end‑to‑end. Use the provided tests/ in Supa‑Crawl‑Chat to verify that search and chat still work after your schema changes. Add new tests for your webhook endpoints: send sample payloads with and without valid signatures and assert that the rows, foreign keys and vector embeddings are correctly stored.

**Summary**

By combining Supa‑Crawl‑Chat’s crawler and search framework with Natural‑DB’s schema, role‑based security and Edge Function patterns, this plan lets you build a full-stack AI ingestion platform.  Official Supabase documentation (CLI, cron, webhooks, RLS, pgvector and environment variables) provides authoritative guidance for each step .