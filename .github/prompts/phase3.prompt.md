---
mode: agent
---

# **PHASE 3: Integration Testing, Deployment & Advanced Schema Patterns**

## **🎯 Task Definition**

**Objective:** Deploy and integrate the Phase 2 components into a production-ready system with advanced security patterns, comprehensive testing, and real-time capabilities for the supa-crawl-chat application.

**Success Criteria:**
- ✅ Edge Functions deployed and tested in production environment
- ✅ Advanced schema patterns implemented with security-first design
- ✅ Comprehensive integration testing suite completed and passing
- ✅ Real-time subscriptions and webhook integrations operational
- ✅ End-to-end application functionality validated
- ✅ Production monitoring and logging configured
- ✅ Security hardening implemented and validated
- ✅ Performance optimization and caching patterns applied

**Prerequisites:**
- ✅ Phase 2 completed: Database schema, RLS policies, and Edge Functions created
- ✅ Migration files ready for deployment (20250902210900_phase2_complete_schema_setup.sql)
- ✅ Edge Functions validated against current Supabase documentation
- ✅ Development environment linked to project thdiqjttovpcehyghaxc

**Constraints:**
- **PRODUCTION READINESS:** All components must be production-tested before deployment
- **SECURITY FIRST:** Multi-schema security patterns with RLS validation
- **COMPREHENSIVE TESTING:** Unit, integration, and end-to-end test coverage
- **DOCUMENTATION COMPLIANCE:** Every implementation cross-referenced with official docs

---

## **🔧 MCP Tool Coordination Strategy**

**MANDATORY: Use ALL MCPs each step:**

| MCP Tool | Purpose | Usage Pattern |
|----------|---------|---------------|
| `sequentialthinking` | Strategic deployment planning and integration design | Plan → Test → Deploy → Validate |
| `supabase` | Production deployment, testing, and validation | Deploy migrations, test functions, validate schemas |
| `brave-search` | Official documentation verification | Verify deployment patterns, security configurations |
| `filesystem` | Test files, documentation, and deployment scripts | Create test suites, document procedures |
| `memory` | Track deployment state and integration patterns | Store deployment lessons, integration decisions |

---

## **📋 Pre-flight Documentation Verification**

**STEP 0: Official Documentation Research & Validation**

**MANDATORY: Use `brave-search` to verify current patterns for:**

1. **Edge Functions Deployment**: https://supabase.com/docs/guides/functions/deploy
2. **Testing Edge Functions**: https://supabase.com/docs/guides/functions/unit-test
3. **Schema Security Hardening**: https://supabase.com/docs/guides/database/hardening-data-api
4. **Custom Schemas**: https://supabase.com/docs/guides/api/using-custom-schemas
5. **Real-time Subscriptions**: https://supabase.com/docs/guides/realtime
6. **Row Level Security Testing**: https://supabase.com/docs/guides/database/postgres/row-level-security
7. **Database Testing with pgTAP**: https://supabase.com/docs/guides/local-development/testing/pgtap-extended

**Use `memory` to store:**
- Current best practices for each documentation area
- Security patterns and RLS testing procedures
- Deployment and CI/CD recommendations
- Real-time integration patterns

**Use `filesystem` to create:**
- Phase 3 pre-flight summary with verified patterns
- Testing strategy document
- Deployment checklist with official citations
/workspaces/Supabase-Services-Hooks-Guide/supa-crawl-chat/.env
---

## **⚡ Step-by-Step Implementation**

### **STEP 1: Advanced Schema Security Implementation**

**1.1 Production Schema Hardening**
**Reference**: [Hardening the Data API](https://supabase.com/docs/guides/database/hardening-data-api)

```sql
-- Create dedicated API schema (verified pattern)
CREATE SCHEMA IF NOT EXISTS api;
GRANT USAGE ON SCHEMA api TO anon, authenticated;

-- Create private schema for internal operations
CREATE SCHEMA IF NOT EXISTS private;
GRANT USAGE ON SCHEMA private TO service_role;

-- Create integrations schema for webhook patterns  
CREATE SCHEMA IF NOT EXISTS integrations;
GRANT USAGE ON SCHEMA integrations TO service_role;
```

**1.2 Schema-based Security Policies**
**Reference**: [Using Custom Schemas](https://supabase.com/docs/guides/api/using-custom-schemas)

```sql
-- Move public tables to API schema with controlled access
CREATE TABLE api.public_pages AS SELECT * FROM public.pages WHERE status = 'published';
ALTER TABLE api.public_pages ENABLE ROW LEVEL SECURITY;

-- Create security definer functions in private schema
CREATE OR REPLACE FUNCTION private.validate_api_access(user_role TEXT)
RETURNS BOOLEAN
SECURITY DEFINER
SET search_path = private
LANGUAGE sql AS $$
  SELECT EXISTS (
    SELECT 1 FROM auth.users 
    WHERE auth.uid() = id AND role = user_role
  );
$$;
```

**MCP Coordination Required:**
- **Use `brave-search`** to verify schema security patterns against official docs
- **Use `supabase`** to implement and test schema permissions
- **Use `filesystem`** to create comprehensive schema migration files
- **Use `memory`** to track security decisions and access patterns

### **STEP 2: Edge Functions Production Deployment**

**2.1 Pre-deployment Testing**
**Reference**: [Testing your Edge Functions](https://supabase.com/docs/guides/functions/unit-test)

Create comprehensive test suite in `supabase/functions/tests/`:

```typescript
// ai-chat-completion-test.ts (verified pattern)
import { assert, assertEquals } from 'jsr:@std/assert@1'
import { createClient, SupabaseClient } from 'npm:@supabase/supabase-js@2'
import 'jsr:@std/dotenv/load'

const testAIChatCompletion = async () => {
  const client = createClient(
    Deno.env.get('SUPABASE_URL') ?? '',
    Deno.env.get('SUPABASE_ANON_KEY') ?? ''
  )

  // Test chat completion function
  const { data, error } = await client.functions.invoke('ai-chat-completion', {
    body: {
      messages: [{ role: 'user', content: 'Hello, test message' }],
      sessionId: 'test-session-123'
    }
  })

  if (error) throw new Error(`Function error: ${error.message}`)
  assert(data.success, 'Chat completion should succeed')
  assert(data.message, 'Should return AI response message')
}

Deno.test('AI Chat Completion Integration', testAIChatCompletion)
```

**2.2 Production Deployment**
**Reference**: [Deploy to Production](https://supabase.com/docs/guides/functions/deploy)

```bash
# Verify authentication (verified command)
supabase login

# Deploy all functions with verification (verified pattern)
supabase functions deploy --project-ref thdiqjttovpcehyghaxc

# Deploy specific function with custom configuration
supabase functions deploy ai-chat-completion --project-ref thdiqjttovpcehyghaxc --verify-jwt
supabase functions deploy webhook-handler --project-ref thdiqjttovpcehyghaxc --no-verify-jwt
```

**2.3 Function Configuration Management**
**Reference**: [Function configuration](https://supabase.com/docs/guides/functions/deploy#function-configuration)

Create `supabase/config.toml` with production settings:
```toml
[functions.ai-chat-completion]
verify_jwt = true

[functions.content-enrichment] 
verify_jwt = true

[functions.webhook-handler]
verify_jwt = false  # For external webhook integration
```

**MCP Coordination Required:**
- **Use `brave-search`** to verify deployment commands and configuration patterns
- **Use `supabase`** to execute deployments and validate function availability
- **Use `filesystem`** to create deployment scripts and configuration files
- **Use `memory`** to track deployment outcomes and performance metrics

### **STEP 3: Comprehensive Database Testing Implementation**

**3.1 pgTAP Testing Suite Setup**
**Reference**: [Advanced pgTAP Testing](https://supabase.com/docs/guides/local-development/testing/pgtap-extended)

```sql
-- 000-setup-tests-hooks.sql (verified pattern)
-- Install pgtap extension for testing
CREATE EXTENSION IF NOT EXISTS pgtap WITH SCHEMA extensions;

-- Install database.dev for test helpers
CREATE EXTENSION IF NOT EXISTS http WITH SCHEMA extensions;
CREATE EXTENSION IF NOT EXISTS pg_tle;

-- Setup test helpers (verified installation pattern)
SELECT pgtle.install_extension(
    'supabase-dbdev',
    resp.contents ->> 'version',
    'PostgreSQL package manager',
    resp.contents ->> 'sql'
) FROM http((
    'GET',
    'https://api.database.dev/rest/v1/package_versions?select=sql,version&package_name=eq.supabase-dbdev&order=version.desc&limit=1',
    ARRAY[('apiKey', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...')::http_header],
    null, null
)) x, LATERAL (SELECT ((row_to_json(x) -> 'content') #>> '{}')::json -> 0) resp(contents);

CREATE EXTENSION "supabase-dbdev";
SELECT dbdev.install('basejump-supabase_test_helpers');
```

**3.2 RLS Policy Testing**
**Reference**: [Row Level Security Testing](https://supabase.com/docs/guides/database/postgres/row-level-security)

```sql
-- 001-rls-security-tests.sql (comprehensive RLS testing)
BEGIN;
SELECT plan(12);

-- Create test users (verified helper pattern)
SELECT tests.create_supabase_user('test_user_1@example.com');
SELECT tests.create_supabase_user('test_user_2@example.com');
SELECT tests.create_supabase_user('admin_user@example.com');

-- Test chat session isolation
SELECT tests.authenticate_as('test_user_1@example.com');

-- User 1 creates a session
INSERT INTO chat_sessions (session_id, user_id) 
VALUES ('test-session-1', tests.get_supabase_uid('test_user_1@example.com'));

-- Test 1: User 1 can see their own session
SELECT results_eq(
  'SELECT count(*) FROM chat_sessions WHERE session_id = ''test-session-1''',
  ARRAY[1::bigint],
  'User can see their own chat session'
);

-- Test 2: User 2 cannot see User 1''s session
SELECT tests.authenticate_as('test_user_2@example.com');
SELECT results_eq(
  'SELECT count(*) FROM chat_sessions WHERE session_id = ''test-session-1''',
  ARRAY[0::bigint],
  'Users cannot see other users'' sessions'
);

-- Additional comprehensive tests for all tables...

SELECT * FROM finish();
ROLLBACK;
```

**3.3 Vector Search Function Testing**

```sql
-- 002-vector-search-tests.sql (vector operations testing)
BEGIN;
SELECT plan(8);

-- Test vector search function with sample data
INSERT INTO sites (name, domain) VALUES ('Test Site', 'test.com');
INSERT INTO pages (site_id, url, title, content) 
SELECT id, 'https://test.com/page1', 'Test Page', 'Sample content for testing'
FROM sites WHERE domain = 'test.com';

-- Insert sample embedding (verified dimensions)
INSERT INTO page_embeddings (page_id, embedding)
SELECT p.id, '[0.1,0.2,0.3]'::vector(1536)
FROM pages p WHERE p.title = 'Test Page';

-- Test vector search function
SELECT results_eq(
  $$SELECT count(*) FROM search_pages_by_embedding('[0.1,0.2,0.3]'::vector(1536), 0.8, 10)$$,
  ARRAY[1::bigint],
  'Vector search function returns expected results'
);

SELECT * FROM finish();
ROLLBACK;
```

**MCP Coordination Required:**
- **Use `supabase`** to execute test suites and validate results
- **Use `filesystem`** to create comprehensive test documentation
- **Use `memory`** to track test coverage and performance benchmarks

### **STEP 4: Real-time Integration Implementation**

**4.1 Real-time Subscriptions Setup**
**Reference**: [Real-time subscriptions](https://supabase.com/docs/guides/realtime)

```sql
-- Enable real-time for specific tables (verified pattern)
ALTER PUBLICATION supabase_realtime ADD TABLE chat_messages;
ALTER PUBLICATION supabase_realtime ADD TABLE pages;
ALTER PUBLICATION supabase_realtime ADD TABLE content_enrichments;
```

**4.2 Client-side Real-time Integration**
**Reference**: [Realtime client integration](https://supabase.com/docs/guides/realtime/quickstart)

```typescript
// Real-time chat integration (verified pattern)
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY)

// Subscribe to chat messages for a session
const subscription = supabase
  .channel('chat-messages')
  .on('postgres_changes', {
    event: 'INSERT',
    schema: 'public',
    table: 'chat_messages',
    filter: `session_id=eq.${sessionId}`
  }, (payload) => {
    console.log('New message:', payload.new)
    updateChatUI(payload.new)
  })
  .subscribe()

// Subscribe to content enrichment updates
const enrichmentSubscription = supabase
  .channel('content-enrichments')
  .on('postgres_changes', {
    event: 'INSERT',
    schema: 'public', 
    table: 'content_enrichments'
  }, (payload) => {
    console.log('Content enriched:', payload.new)
    updateContentDisplay(payload.new)
  })
  .subscribe()
```

**4.3 Webhook Integration Testing**

```typescript
// webhook-integration-test.ts (comprehensive webhook testing)
const testWebhookHandler = async () => {
  const webhookPayload = {
    event: 'page_processed',
    data: {
      site_id: 'test-site-id',
      url: 'https://example.com/test',
      title: 'Test Page',
      content: 'Sample content for webhook testing',
      status_code: 200
    }
  }

  const response = await fetch(`${SUPABASE_URL}/functions/v1/webhook-handler`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${SUPABASE_SERVICE_ROLE_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(webhookPayload)
  })

  const data = await response.json()
  assert(data.success, 'Webhook should process successfully')
  assert(data.enrichment, 'Should trigger content enrichment')
}
```

**MCP Coordination Required:**
- **Use `brave-search`** to verify real-time patterns and subscription syntax
- **Use `supabase`** to configure real-time settings and test subscriptions
- **Use `filesystem`** to create integration test suites
- **Use `memory`** to track real-time performance and connection patterns

### **STEP 5: Production Validation & Monitoring**

**5.1 End-to-End Application Testing**

Create comprehensive integration test suite:

```typescript
// e2e-application-tests.ts (full application testing)
import { test } from 'jsr:@std/testing/bdd'

test('Complete crawl-to-chat workflow', async () => {
  const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY)
  
  // 1. Create site and trigger crawl
  const { data: site } = await supabase
    .from('sites')
    .insert({ name: 'Test Site', domain: 'example.com' })
    .select()
    .single()
  
  // 2. Simulate webhook for page processing
  const webhookResponse = await fetch(`${SUPABASE_URL}/functions/v1/webhook-handler`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      event: 'page_processed',
      data: {
        site_id: site.id,
        url: 'https://example.com/test',
        title: 'Test Content',
        content: 'This is test content for semantic search.'
      }
    })
  })
  
  // 3. Verify content enrichment occurred
  await new Promise(resolve => setTimeout(resolve, 5000)) // Wait for async processing
  
  const { data: enrichments } = await supabase
    .from('content_enrichments')
    .select('*')
    .eq('enrichment_type', 'summary')
  
  assert(enrichments.length > 0, 'Content should be enriched')
  
  // 4. Test AI chat with enriched content
  const { data: chatResponse } = await supabase.functions.invoke('ai-chat-completion', {
    body: {
      messages: [{ role: 'user', content: 'Tell me about the test content' }],
      sessionId: 'e2e-test-session'
    }
  })
  
  assert(chatResponse.success, 'Chat should work with enriched content')
})
```

**5.2 Performance Monitoring Setup**
**Reference**: [Database performance](https://supabase.com/docs/guides/database/performance)

```sql
-- Performance monitoring queries (verified patterns)
-- Monitor query performance
SELECT 
  query, 
  calls, 
  total_time, 
  mean_time,
  rows
FROM pg_stat_statements 
WHERE query LIKE '%chat_messages%' 
ORDER BY total_time DESC;

-- Monitor index usage
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan,
  idx_tup_read,
  idx_tup_fetch
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan DESC;

-- Vector search performance monitoring
EXPLAIN ANALYZE SELECT * FROM search_pages_by_embedding('[0.1,0.2,0.3]'::vector(1536), 0.5, 10);
```

**5.3 Security Validation**

```sql
-- Security audit queries (comprehensive security testing)
-- Verify RLS is enabled on all public tables
SELECT schemaname, tablename, rowsecurity 
FROM pg_tables 
WHERE schemaname = 'public' AND rowsecurity = false;

-- Audit user permissions
SELECT 
  r.rolname,
  n.nspname as schema_name,
  c.relname as table_name,
  p.privilege_type
FROM information_schema.role_table_grants p
JOIN pg_class c ON c.relname = p.table_name
JOIN pg_namespace n ON n.oid = c.relnamespace  
JOIN pg_roles r ON r.rolname = p.grantee
WHERE n.nspname IN ('public', 'api', 'private')
ORDER BY r.rolname, n.nspname, c.relname;
```

**MCP Coordination Required:**
- **Use `supabase`** to execute performance queries and security audits
- **Use `filesystem`** to create monitoring dashboard and alerts
- **Use `memory`** to track performance baselines and security configurations

---

## **📊 Quality Assurance & Production Readiness**

### **STEP 6: Comprehensive System Validation**

**Before marking Phase 3 complete, verify:**

**Database & Schema:**
- [ ] **Schema Security**: Custom schemas implemented with proper permissions (verified against [Schema Hardening docs](https://supabase.com/docs/guides/database/hardening-data-api))
- [ ] **RLS Validation**: All policies tested and passing (verified against [RLS Testing docs](https://supabase.com/docs/guides/database/postgres/row-level-security))
- [ ] **Performance**: Query performance optimized and monitored
- [ ] **Data Integrity**: Foreign key constraints and validation rules tested

**Edge Functions:**
- [ ] **Deployment**: All functions deployed and accessible (verified against [Deployment docs](https://supabase.com/docs/guides/functions/deploy))
- [ ] **Testing**: Comprehensive test suites passing (verified against [Testing docs](https://supabase.com/docs/guides/functions/unit-test))
- [ ] **Configuration**: Production configurations applied and validated
- [ ] **Error Handling**: Comprehensive error handling and logging implemented

**Integration & Real-time:**
- [ ] **Subscriptions**: Real-time subscriptions operational and tested
- [ ] **Webhooks**: Webhook handlers processing events correctly
- [ ] **Client Integration**: Frontend successfully integrating with all APIs
- [ ] **End-to-End**: Complete user workflows tested and functional

**Security & Monitoring:**
- [ ] **Access Control**: User roles and permissions properly configured
- [ ] **API Security**: Rate limiting and authentication properly configured
- [ ] **Monitoring**: Performance monitoring and alerting operational
- [ ] **Audit Logs**: Security audit procedures documented and tested

**Documentation:**
- [ ] **API Documentation**: All endpoints documented with examples
- [ ] **Deployment Guides**: Production deployment procedures documented
- [ ] **Security Procedures**: Security validation checklists created
- [ ] **Troubleshooting**: Common issues and solutions documented

---

## **🚀 Success Metrics & Validation**

**Technical Metrics:**
- Database query response times < 100ms for 95th percentile
- Edge Function cold start times < 2 seconds
- Real-time subscription latency < 500ms
- Zero security policy violations in audit
- 100% test coverage for critical paths

**Functional Metrics:**
- Complete crawl-to-chat workflow operational
- AI responses generated with enriched content context
- Real-time updates delivered to connected clients
- Webhook processing handling expected load
- Search functionality returning relevant results

**Operational Metrics:**  
- Deployment pipeline automated and validated
- Monitoring dashboards operational with alerts
- Security scanning integrated and passing
- Performance baselines established and documented
- Rollback procedures tested and validated

---

**⚠️ CRITICAL:** Do not proceed beyond Phase 3 until ALL integration tests pass, security validation is complete, and production monitoring is operational.

**Documentation Sources Referenced:**
- [Edge Functions Deployment](https://supabase.com/docs/guides/functions/deploy) - Production deployment patterns
- [Testing Edge Functions](https://supabase.com/docs/guides/functions/unit-test) - Comprehensive testing approaches  
- [Schema Security Hardening](https://supabase.com/docs/guides/database/hardening-data-api) - Production security patterns
- [Custom Schemas](https://supabase.com/docs/guides/api/using-custom-schemas) - Multi-schema architecture
- [Real-time Integration](https://supabase.com/docs/guides/realtime) - Live data subscriptions
- [Advanced pgTAP Testing](https://supabase.com/docs/guides/local-development/testing/pgtap-extended) - Database testing patterns
- [Row Level Security](https://supabase.com/docs/guides/database/postgres/row-level-security) - Security policy implementation

---