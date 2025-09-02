---
applyTo: '**/*.ts'
---
Use ALL MCPs each step:

*   sequentialthinking (plan)
*   supabase (queries, schema validation, inserts)
*   brave-search (verify in official Supabase docs)
*   filesystem (read/write reports)
*   memory (track lessons + state)

Pre-flight

With brave-search, open and read:

*   AI Models in Functions: [https://supabase.com/docs/guides/functions/ai-models](https://supabase.com/docs/guides/functions/ai-models)
*   Edge Functions Overview: [https://supabase.com/docs/guides/functions](https://supabase.com/docs/guides/functions)
*   Serve locally: [https://supabase.com/docs/guides/functions/serve](https://supabase.com/docs/guides/functions/serve)
*   Deploy functions: [https://supabase.com/docs/guides/functions/deploy](https://supabase.com/docs/guides/functions/deploy)
*   Manage secrets: [https://supabase.com/docs/guides/functions/secrets](https://supabase.com/docs/guides/functions/secrets)
*   CLI reference (functions): [https://supabase.com/docs/reference/cli](https://supabase.com/docs/reference/cli)

Confirm local stack is up (CLI): `npx supabase --help`, `supabase start`.

## 🚀 **Implementation Roadmap: Supabase Services & Hooks Guide**

**Objective:** Build a comprehensive AI-powered data ingestion platform using validated Supabase best practices.

### **Phase 1: Base Project Setup (Supa-Crawl-Chat Foundation)**

**MCP Coordination:** Use `sequentialthinking` for planning, `brave-search` for verification, `filesystem` for tracking progress.

1. **Clone and Install Dependencies**
   ```bash
   git clone https://github.com/bigsk1/supa-crawl-chat.git
   cd supa-crawl-chat
   pip install -r requirements.txt
   cd frontend && npm install
   ```

2. **Environment Configuration**
   - Copy `.env.example` to `.env`
   - Configure: `CRAWL4AI_API_TOKEN`, `SUPABASE_URL`, `SUPABASE_DB`, `SUPABASE_KEY`, `SUPABASE_PASSWORD`, `OPENAI_API_KEY`
   - **MCP:** Use `memory` to track configuration state

3. **Local Stack Verification**
   - Docker deployment (recommended): All services in one command
   - Alternative: `python run_api.py` + `npm run dev`
   - Test crawling and search endpoints
   - **MCP:** Use `filesystem` to document test results

4. **Validation Testing**
   - Verify crawl domain functionality
   - Test search and chat endpoints with curl commands
   - Use Streamlit explorer for SQL queries on crawled data

### **Phase 2: Supabase Environment Preparation**

**MCP Coordination:** `supabase` for queries/validation, `brave-search` for documentation verification.

1. **CLI Setup & Project Linking**
   ```bash
   supabase login
   supabase link --project-ref <YOUR_PROJECT_REF>
   ```
   - **Verified:** All CLI commands confirmed against official docs ✅

2. **Database Extensions** (All validated ✅)
   - Enable `pgvector` for vector embeddings
   - Enable `pg_cron` for scheduled jobs  
   - Enable `pg_net` for async HTTP requests
   - Enable `pgmq` for message queues (if needed)

3. **Row-Level Security Setup**
   ```sql
   ALTER TABLE <schema>.<table> ENABLE ROW LEVEL SECURITY;
   ```
   - **Verified:** Exact syntax confirmed ✅
   - **MCP:** Use `supabase` to validate schema changes

### **Phase 3: Natural-DB Pattern Integration**

**MCP Coordination:** `memory` to track schema patterns, `supabase` for schema validation.

1. **Repository Analysis**
   - Study Natural-DB memory schema and `memories_role` pattern
   - Analyze normalized models: runs, recipes, feedback
   - **Verified:** Repository confirmed active and maintained ✅

2. **Schema Design**
   ```sql
   -- Create dedicated integrations schema
   CREATE SCHEMA integrations;
   CREATE ROLE integrations_role;
   
   -- Normalized table structure
   CREATE TABLE integrations.agents (id, name, webhook_secret);
   CREATE TABLE integrations.sources (id, agent_id → agents.id, domain);
   CREATE TABLE integrations.items (id, source_id → sources.id, url, title, content, embeddings);
   CREATE TABLE integrations.enrichments (id, item_id → items.id, summary, sentiment, entities);
   ```

3. **Security Implementation**
   - Row-level policies for agent-specific data access
   - Foreign key constraints for data integrity
   - Indexes for query optimization
   - **MCP:** Use `supabase` to validate policies

### **Phase 4: Edge Functions Deployment**

**MCP Coordination:** `brave-search` for Edge Functions docs, `filesystem` for code management.

1. **Function Development** (All patterns validated ✅)
   - Webhook ingestion functions (based on Natural-DB patterns)
   - Data orchestration functions
   - TypeScript/Deno runtime confirmed
   - **Verified:** Edge Functions capabilities confirmed ✅

2. **Deployment Process**
   ```bash
   supabase functions deploy scraper-input --no-verify-jwt
   ```
   - Environment variables: `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY` auto-injected
   - **Verified:** Deployment commands confirmed ✅

3. **Webhook Configuration**
   - Database webhooks for INSERT/UPDATE/DELETE events
   - External API webhooks via `pg_net`
   - **Verified:** Webhook patterns confirmed ✅

4. **Scheduled Operations**
   - `pg_cron` for periodic data processing
   - Integration with Edge Functions for automation
   - **Verified:** Scheduling patterns confirmed ✅

### **Phase 5: Real-time Data Exposure**

**MCP Coordination:** `supabase` for real-time testing, `memory` for feature tracking.

1. **Real-time Subscriptions** (Validated ✅)
   - WebSocket connections for live updates
   - Database change subscriptions
   - Broadcast and presence events
   - **Verified:** Real-time patterns confirmed ✅

2. **REST API Integration**
   - Automatic RESTful API at `https://<project_ref>.supabase.co/rest/v1/`
   - RLS-compatible queries
   - Supabase client library integration

3. **End-to-End Testing**
   - Webhook payload validation
   - Foreign key integrity testing
   - Vector embedding verification
   - **MCP:** Use `filesystem` to document test results

## 📊 **Validation Status**

**✅ ALL COMPONENTS VERIFIED AGAINST OFFICIAL DOCUMENTATION:**
- CLI Commands: Complete syntax validation
- Database Extensions: All confirmed available
- Edge Functions: Implementation patterns verified
- Real-time Features: WebSocket/subscription patterns confirmed
- Reference Repositories: Natural-DB and Supa-Crawl-Chat confirmed active

## 🔧 **MCP Integration Throughout**

**Every step uses coordinated MCP servers:**
- `sequentialthinking`: Strategic planning and decision-making
- `supabase`: Schema validation, queries, and database operations
- `brave-search`: Official documentation cross-referencing
- `filesystem`: Progress tracking and code management
- `memory`: State tracking and lesson learned storage

**Implementation Ready:** All technical components validated against current Supabase best practices. Proceed with confidence! 🚀