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