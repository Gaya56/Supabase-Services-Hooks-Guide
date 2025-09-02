````prompt
---
mode: agent
---

# **PHASE 1: Supa-Crawl-Chat Foundation Setup**

## **🎯 Task Definition**

**Objective:** Establish the Supa-Crawl-Chat foundation as the base layer for our Supabase Services & Hooks Guide implementation.

**Success Criteria:**
- ✅ Repository successfully cloned and verified against official source
- ✅ All dependencies installed and version-validated
- ✅ Environment configuration completed with official Supabase credentials
- ✅ Local development stack running and fully functional
- ✅ Crawling and search functionality validated through testing
- ✅ All steps documented with official source cross-references

**Constraints:**
- **SLOW & METHODICAL:** Each step must be verified before proceeding
- **OFFICIAL DOCS ONLY:** All configurations cross-referenced with official documentation
- **COMPLETE DOCUMENTATION:** Every action tracked and reported
- **NO ASSUMPTIONS:** Verify every command, URL, and configuration

---

## **🔧 MCP Tool Coordination Strategy**

**MANDATORY: Use ALL MCP tools systematically:**

| MCP Tool | Purpose | Usage Pattern |
|----------|---------|---------------|
| `sequentialthinking` | Strategic planning for each phase | Plan → Execute → Verify → Document |
| `brave-search` | Official documentation verification | Cross-reference every step with official sources |
| `filesystem` | Progress tracking and documentation | Document findings, create reports, track state |
| `memory` | State management and lessons learned | Store configuration details, errors, solutions |
| `supabase` | Database validation (when applicable) | Validate credentials, test connections |

---

## **📋 Pre-Implementation Verification Phase**

**STEP 0: Repository & Documentation Verification**

1. **Use `brave-search`** to verify:
   - Supa-Crawl-Chat repository exists at `https://github.com/bigsk1/supa-crawl-chat`
   - Repository is active and maintained (recent commits, issues, stars)
   - README documentation is current and complete
   - Required dependencies are clearly listed

2. **Use `sequentialthinking`** to plan:
   - Identify all system requirements
   - Plan dependency installation order
   - Anticipate potential configuration challenges
   - Define verification checkpoints

3. **Use `memory`** to store:
   - Repository metadata and status
   - Planned implementation approach
   - Initial assessment findings

---

## **⚡ Step-by-Step Implementation**

### **STEP 1: Repository Acquisition**

**1.1 Clone Repository**
```bash
git clone https://github.com/bigsk1/supa-crawl-chat.git
cd supa-crawl-chat
```

**Verification Required:**
- **Use `filesystem`** to confirm successful clone
- **Use `filesystem`** to list directory contents and verify structure
- **Use `brave-search`** to cross-reference directory structure with official README

**1.2 Environment Analysis**
- **Use `filesystem`** to read and analyze `requirements.txt`
- **Use `filesystem`** to read and analyze `package.json` (frontend)
- **Use `filesystem`** to examine `.env.example`
- **Use `memory`** to store dependency analysis results

### **STEP 2: Dependency Installation**

**2.1 Python Dependencies**
```bash
pip install -r requirements.txt
```

**Verification Required:**
- **Use `sequentialthinking`** to plan installation strategy
- Document any version conflicts or installation errors
- **Use `memory`** to store successful package versions
- **Use `filesystem`** to create installation report

**2.2 Frontend Dependencies**
```bash
cd frontend && npm install
```

**Verification Required:**
- Confirm Node.js version compatibility
- Document any npm warnings or errors
- **Use `filesystem`** to verify `node_modules` creation
- **Use `memory`** to store frontend setup status

### **STEP 3: Environment Configuration**

**3.1 Configuration File Setup**
```bash
cp .env.example .env
```

**3.2 Credential Configuration**
**REQUIRED VARIABLES (cross-reference with official Supabase docs):**
- `SUPABASE_URL` - **Use `brave-search`** to verify format requirements
- `SUPABASE_DB` - **Use `brave-search`** to confirm naming conventions  
- `SUPABASE_KEY` - **Use `brave-search`** to validate key types (anon vs service_role)
- `SUPABASE_PASSWORD` - **Use `supabase`** to verify connection if possible
- `OPENAI_API_KEY` - **Use `brave-search`** to confirm OpenAI key format
- `CRAWL4AI_API_TOKEN` - Verify if required or has defaults

**Verification Required:**
- **Use `supabase`** to test database connection (if credentials available)
- **Use `memory`** to store configuration state (without sensitive data)
- **Use `filesystem`** to document configuration process

### **STEP 4: Local Stack Deployment**

**4.1 Docker Deployment (Recommended)**
**Research Required:**
- **Use `brave-search`** to verify Docker requirements in repository README
- **Use `filesystem`** to examine docker-compose or deployment scripts
- **Use `sequentialthinking`** to plan deployment sequence

**4.2 Alternative Manual Deployment**
```bash
# Backend
python run_api.py

# Frontend (separate terminal)
npm run dev
```

**Verification Required:**
- **Use `filesystem`** to monitor process logs
- Confirm all services are running on expected ports
- **Use `memory`** to document successful deployment configuration

### **STEP 5: Functionality Validation**

**5.1 Crawling Functionality Test**
- **Use `brave-search`** to find official README test commands
- Execute crawl commands as documented
- **Use `filesystem`** to document test results

**5.2 Search & Chat Endpoint Testing**
- **Use `brave-search`** to locate official curl examples
- Test search endpoints with provided examples
- Test chat endpoints with sample queries
- **Use `filesystem`** to log all test responses

**5.3 Streamlit Explorer Verification**
- Access SQL query interface
- **Use `supabase`** to validate database queries (if possible)
- Test data retrieval and visualization
- **Use `memory`** to store successful query patterns

---

## **📊 Documentation & Reporting Requirements**

**MANDATORY DELIVERABLES:**

1. **Installation Report** (`filesystem`)
   - Complete dependency list with versions
   - Any installation issues and resolutions
   - System requirements verification

2. **Configuration Report** (`filesystem`)  
   - Environment variable requirements
   - Official documentation cross-references
   - Connection test results

3. **Functionality Report** (`filesystem`)
   - All test results with timestamps
   - API endpoint responses
   - Error logs and resolutions

4. **Lessons Learned** (`memory`)
   - Configuration challenges encountered
   - Successful resolution strategies
   - Recommendations for future implementations

---

## **🔍 Quality Assurance Checkpoints**

**Before marking this phase complete, verify:**

- [ ] All official documentation cross-referenced with `brave-search`
- [ ] Complete installation documented with `filesystem`
- [ ] Configuration state tracked with `memory`
- [ ] Database connectivity validated with `supabase` (if possible)
- [ ] All test results documented and verified
- [ ] No assumptions made - everything explicitly verified

**⚠️ CRITICAL:** Do not proceed to Phase 2 until ALL checkpoints are satisfied and documented.

---

## **🎯 Success Validation**

**Final verification must confirm:**
1. Supa-Crawl-Chat is fully operational in local environment
2. All features tested and documented
3. Configuration is reproducible and documented
4. Any modifications or customizations are tracked
5. System is ready for Supabase Services integration (Phase 2)

**PROCEED SLOWLY. VERIFY EVERYTHING. DOCUMENT COMPLETELY.**
````