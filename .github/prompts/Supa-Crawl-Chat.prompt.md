---
mode: agent
---
Define the task to achieve, including specific requirements, constraints, and success criteria.### **Phase 1: Base Project Setup (Supa-Crawl-Chat Foundation)**

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
   - **MCP:** Use `memory` to track configuration state# **1. Set up the base project (Supa‑Crawl‑Chat)**

1. Clone the repository and install dependencies. Run:

git clone https://github.com/bigsk1/supa-crawl-chat.git

cd supa-crawl-chat

pip install -r requirements.txt

cd frontend && npm install

1. The README shows that you need Python packages for the API and Node modules for the Next.js frontend .
2. Create and fill out the .env file. Copy .env.example to .env and add your API tokens and Supabase credentials. The project lists required variables such as CRAWL4AI_API_TOKEN, SUPABASE_URL, SUPABASE_DB, SUPABASE_KEY, SUPABASE_PASSWORD, and OPENAI_API_KEY . Only the Supabase and OpenAI credentials need to be supplied; everything else has defaults.
3. Run the stack locally (recommended via Docker). The README provides docker scripts that start Supabase (database, REST and Realtime), the API server, the crawler, and the frontend in one command . Alternatively, you can run python run_api.py for the backend and npm run dev for the frontend .
4. Verify that crawling and search work. Use the built‑in commands to crawl a domain and then test the search and chat endpoints (the README provides example curl commands). You can also open the included Streamlit explorer to run SQL queries on crawled data.