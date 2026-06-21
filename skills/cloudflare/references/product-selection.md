# Product Selection

Use this file when the user asks a broad Cloudflare architecture, storage, compute, AI, networking, security, analytics, or deployment question and the right product is not obvious.

## Retrieval Rule

Use this as a selection checklist, not an API reference. Retrieve current Cloudflare docs before exact commands, limits, pricing, configuration fields, beta status, or API shapes.

## Prefer Focused Skills

Load or follow focused skills first when they match:

| Task | Prefer |
| --- | --- |
| Wrangler commands, config, resource creation | `wrangler` |
| Workers code review or production Worker patterns | `workers-best-practices` |
| Durable Objects, SQLite per object, WebSockets | `durable-objects` |
| Agents SDK, chat agents, MCP, scheduling | `agents-sdk` |
| Sandbox SDK, code execution, interpreter environments | `sandbox-sdk` |
| Transactional email sending or routing | `cloudflare-email-service` |
| Turnstile setup or CAPTCHA migration | `turnstile-spin` |
| Cloudflare One or SASE/Zero Trust | `cloudflare-one` or `cloudflare-one-migrations` |

## Common Choices

| Need | Default choice | Use something else when |
| --- | --- | --- |
| Stateless HTTP/API code | Workers | The app is static-first with Git deploys; consider Pages or Workers static assets. |
| Static or full-stack frontend | Workers static assets or Pages | Existing Pages project conventions dominate. |
| Per-room, per-user, per-tenant coordination | Durable Objects | Data needs global relational querying; use D1 or external DB. |
| Key-value config, cache, feature flags | KV | You need read-after-write consistency, relational queries, or high write rate to one key. |
| Relational SQLite data | D1 | Large binary/object data belongs in R2; existing Postgres/MySQL may fit Hyperdrive. |
| Object/file storage | R2 | You need relational indexes or transactions; pair with D1/DO metadata. |
| Data lake tables in object storage | R2 Data Catalog | Plain object storage is enough, or the workload is transactional rather than analytical. |
| SQL over R2/Iceberg data | R2 SQL | The data is small, operational, or latency-sensitive; use D1, Analytics Engine, or an external warehouse. |
| Streaming ingest into R2/Iceberg | Pipelines | You only need a task queue; use Queues, or use Analytics Engine for custom metrics. |
| Background jobs | Queues | Multi-step durable orchestration with checkpoints fits Workflows. |
| Multi-step durable business process | Workflows | One-off async fanout may be simpler with Queues. |
| Linux/runtime-specific workloads | Containers | Plain Workers or the Sandbox SDK can satisfy the runtime, CPU, filesystem, or isolation need. |
| Vector search | Vectorize | Managed RAG over a website, R2 bucket, or uploaded documents may fit AI Search. |
| LLM/image/embedding inference on Cloudflare | Workers AI | You need provider routing/caching/observability across multiple AI providers; use AI Gateway. |
| AI provider gateway, caching, routing, logs | AI Gateway | You only need Workers AI binding calls in one Worker. |
| Shared reusable secrets | Secrets Store | A per-Worker Wrangler secret is simpler and rotation/sharing requirements are modest. |
| Existing Postgres/MySQL from Workers | Hyperdrive | Edge-native SQLite is enough; use D1. |
| Private app/network access, WARP, Gateway, Access | Cloudflare One | Simple public HTTP reverse proxy may only need Tunnel or Workers. |
| Non-HTTP TCP/UDP proxy | Spectrum | HTTP/HTTPS traffic should normally use Cloudflare proxy/Workers. |
| Real-time audio/video or TURN | Realtime docs and TURN docs | Video-on-demand belongs in Stream; stateful chat often belongs in Durable Objects/WebSockets. |
| Browser automation, screenshots, PDFs | Browser Rendering | Simple HTTP/API scraping can use `fetch()`. |
| Image storage and transformation | Images | Raw object storage with custom processing can use R2 plus Workers. |
| Video storage/streaming | Stream | Small static video assets may be enough in R2 or static assets. |
| Built-in Cloudflare analytics queries | GraphQL Analytics API | Custom high-cardinality application metrics fit Analytics Engine. |
| Custom application metrics | Analytics Engine | Debug logs/traces fit Workers observability. |
| Website traffic analytics or tag orchestration | Web Analytics or Zaraz | Product analytics, security analytics, or Worker metrics need other analytics products. |
| Infrastructure as code | Terraform or Pulumi | App deploy loops often stay simpler with Wrangler. Do not co-manage the same resource from both. |

## Product Fit Notes

- Prefer bindings from Workers code over calling Cloudflare REST APIs from inside Workers.
- Use Wrangler for local reproducibility, generated types, and developer workflows.
- Use Cloudflare REST APIs for management-plane automation that Wrangler does not cover.
- Use Terraform/Pulumi for declarative infrastructure ownership, not for high-frequency app deploys unless the team already owns that flow.
- Use GraphQL Analytics API for Cloudflare product analytics; use Analytics Engine for metrics your Worker writes.
- Use R2 for binary/blob payloads; pair it with D1 or Durable Objects for transactional metadata, or KV for read-heavy metadata where eventual consistency is acceptable.
- Use Durable Objects for coordination and consistency boundaries; use Queues or Workflows for asynchronous work.
- Use Workers for Platforms only when customers or tenants deploy code; do not use it for ordinary multitenancy.

## Validation Defaults

After choosing products, validate with the closest current checks:

```bash
npx wrangler types
npx wrangler deploy --dry-run
npm test
npx tsc --noEmit
```

For product-specific resources, retrieve the current Wrangler help before creating or deleting anything:

```bash
npx wrangler <resource> --help
```
