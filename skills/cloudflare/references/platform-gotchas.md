# Platform Gotchas

## Contents
- How To Use This File
- Cross-Cutting
- Compute And Runtime
- Storage And Data
- Async And Orchestration
- AI And Search
- Analytics And Observability
- Networking And Security
- Deployment And IaC
- Local Development

Use this file as a compact checklist of durable, agent-relevant failure modes. It intentionally avoids complete API examples, command inventories, limits tables, and pricing details. Retrieve current Cloudflare docs before exact values or syntax.

## How To Use This File

Before implementing or debugging a product without a focused skill:

1. Read the relevant section below.
2. Retrieve current product docs.
3. Inspect project config and generated types.
4. Apply the smallest fix.
5. Validate with Wrangler, tests, typecheck, or product-specific inspection.

## Cross-Cutting

- Generate types from config with `npx wrangler types`; do not hand-write binding interfaces in TypeScript projects.
- Keep secret values out of source, config, logs, shell history, and chat. Prefer Wrangler secrets or platform secret stores.
- Prefer runtime bindings from Workers code over Cloudflare REST API calls when a binding exists.
- Distinguish local simulations from remote resources. A local pass does not prove production bindings, permissions, or regional behavior.
- Confirm account, zone, worker, environment, and resource names before writes or deletes.
- For new or uncommon Wrangler commands, retrieve `npx wrangler <group> --help` first.
- Do not cache `env`, bindings, or secret values in module scope. Read them from the handler, Durable Object instance, or current request path.

## Compute And Runtime

### Workers Runtime

- Request and response bodies are streams. Read them once, or clone/tee before multiple consumers.
- Do not rely on module-scope variables for durable state. Use Durable Objects, D1, KV, R2, or external storage based on consistency needs.
- Keep runtime-bound calls such as `fetch()`, binding access, and secret reads inside handlers or object methods, not module initialization.
- Node.js APIs require current compatibility guidance. Prefer Workers-native APIs when the runtime feature is available without `nodejs_compat`.
- For Workers code review, prefer the focused `workers-best-practices` skill.

### Containers

- Containers run through a `Container` class that extends Durable Objects; use Durable Object storage for state that must survive container restarts.
- Container disk is ephemeral by default. Persist important outputs to Durable Object storage, R2, D1, or an external store.
- In an overridden container `fetch()`, call `containerFetch()` to proxy HTTP requests to the container and avoid recursive `this.fetch()` loops.
- `containerFetch()` does not support WebSockets; use the container `fetch()` path for WebSocket proxying and retrieve the current WebSocket example.
- Configure readiness deliberately with `defaultPort`, `requiredPorts`, and current startup APIs. Starting a container is not the same as proving every service is ready.
- If overriding idle/activity hooks, stop or destroy the container when intended; otherwise idle containers can keep renewing instead of sleeping.

### Placement And Latency

- Evaluate Smart Placement or explicit Placement Hints when a Worker makes multiple sequential round trips to a single backend region.
- Do not enable placement blindly on monolithic full-stack Workers, static-asset-heavy apps, or globally distributed backends; measure the request path first.
- Placement behavior, headers, beta status, and limitations change. Retrieve current docs before relying on `cf-placement` or exact eligibility rules.

## Storage And Data

### KV

- KV is eventually consistent. Avoid workflows that require immediate global read-after-write.
- Avoid hot-key write patterns. If many requests update the same logical value, use Durable Objects or a queue-backed aggregation pattern.
- Treat missing keys as expected; KV returns null for absent values.
- Use prefixes and metadata intentionally so list operations stay bounded and debuggable.

### D1

- Use prepared statements and bound parameters. For dynamic filters, build SQL from allowlisted column/order fragments and bind all user-controlled values.
- Production migrations require the remote database target; local migrations alone do not update production.
- SQLite types matter: represent booleans, dates, and JSON deliberately instead of assuming database-native types.
- Use indexes for frequent predicates, joins, and sort keys; verify with `EXPLAIN QUERY PLAN`, and avoid indexes with no query demand.
- Prefer keyset/cursor pagination for large ordered result sets; offset pagination is acceptable for small or admin-only tables.
- Avoid N+1 query loops. Use joins for related rows, and use `batch()` for independent statements that can share a round trip.
- Chunk large writes and imports; retrieve current batch, timeout, and import/export limits before sizing the chunks.
- Cache D1 reads only when staleness is acceptable. Give KV/cache entries explicit TTLs and update or invalidate them after writes when users expect fresh reads.
- Choose tenancy deliberately: shared database plus `tenant_id` for global queries and simple operations; per-tenant databases for stronger isolation or scale boundaries. Never derive binding names directly from untrusted tenant input without an allowlist.
- For global read replication, retrieve current D1 docs before coding. Do not invent a separate replica binding; current docs route replica reads through the D1 Sessions API.
- Retrieve current docs before using Time Travel, backups, Sessions API, pricing, limits, or beta features.
- Store binary/blob data in R2 and keep D1 for relational metadata.
- Catch uniqueness and constraint errors and translate them into user-meaningful responses.

### R2

- R2 is object storage, not a database. Store searchable metadata elsewhere.
- Design object keys and prefixes for lifecycle, listing, and tenant isolation.
- Public access, custom domains, CORS, and signed URLs are separate concerns; retrieve current setup docs before wiring them.
- For S3-compatible clients, verify endpoint, region expectations, and credential scope.
- When listing, follow `truncated` and `cursor`; do not assume a page contains the requested limit or compare object counts.
- Use `httpEtag` when returning an R2 object's ETag as an HTTP header.
- Treat presigned URLs as bearer tokens. Return expiry metadata to clients and keep expirations short for sensitive operations.
- For multipart uploads, handle missing or completed upload IDs and abort/cleanup failures explicitly; resumed upload handles do not prove the upload still exists.

### Hyperdrive

- Use Hyperdrive for existing Postgres/MySQL from Workers; use D1 for edge-native SQLite.
- Close or release database clients correctly, especially around errors.
- For multiple sequential database calls, evaluate Smart Placement so the Worker runs near the database.
- Cacheability depends on query shape and driver behavior; retrieve current docs before promising query caching.

### Vectorize

- Match distance metric and dimensions to the embedding model.
- Keep source documents or object keys outside Vectorize when content needs retrieval; vectors alone are not full document storage.
- Rebuild or version indexes when changing embedding models.
- Treat metadata filters as part of schema design, not an afterthought.

## Async And Orchestration

### Queues

- Queue delivery is at-least-once. Make consumers idempotent.
- Handle each message explicitly. An uncaught error can retry more than the one message you intended.
- Acknowledge skipped or intentionally ignored messages.
- Choose message content type based on consumers. Prefer JSON when dashboard inspection, HTTP pull consumers, or non-Worker consumers need to read payloads.
- Use dead-letter queues and error classification for poison messages.
- Do not use `batch.messages.forEach(async ...)`; use `for...of` with `await` or `Promise.all()` so processing completes before the handler returns.
- If a producer sends messages with `ctx.waitUntil()`, catch and log send failures because the HTTP response will not naturally surface them.

### Workflows

- Design steps to be idempotent and restart-safe.
- Persist critical final outputs to durable storage before instance retention windows matter.
- Use Workflows for durable multi-step orchestration, not as a substitute for every background job.
- Validate workflow names, event types, and bindings against current docs and generated types.

### Cron Triggers

- Scheduled handlers need explicit error handling and idempotency.
- Use a durable execution ID or lock if missed, duplicated, or overlapping runs would hurt correctness.
- Test scheduled handlers locally only as a smoke test; validate deployed behavior separately.

## AI And Search

### Workers AI

- Retrieve current model IDs, inputs, output shapes, and SDK guidance before coding.
- Keep model choice configurable when product behavior depends on quality, cost, or latency.
- Handle provider/runtime errors with retries or fallbacks appropriate to the user workflow.

### AI Gateway

- Use AI Gateway when routing, caching, observability, rate limiting, provider fallback, or key management across AI providers matters.
- Verify provider URL patterns and SDK integration against current docs.
- Keep provider API keys in secrets, not request code or committed config.

### AI Search

- Use AI Search for managed RAG when the source is a website, R2 bucket, or uploaded documents and managed indexing/retrieval is enough.
- Retrieve current AI Search indexing and sync behavior before promising freshness, deletion behavior, or crawl timing.
- For custom ingestion, scoring, or metadata schema control beyond AI Search settings, use Vectorize plus your own retrieval pipeline.

## Analytics And Observability

### GraphQL Analytics API

- Always include time filters and request only needed fields.
- Responses can be HTTP 200 with GraphQL errors; inspect `errors`, not just status.
- Adaptive datasets can be sampled. Treat results as representative unless docs/schema prove exactness for the dataset.
- Use introspection or docs to verify dataset names, dimensions, scopes, and permissions.

### Analytics Engine

- Use it for custom high-cardinality metrics from Workers, not for querying built-in Cloudflare product analytics.
- Design indexes/blobs before writing data; query flexibility depends on what you wrote.
- Account for sampling and retention by retrieving current docs.
- Treat writes as telemetry emission, not transactional confirmation. Query Analytics Engine through its analytics/query surfaces, not from the Worker binding that writes data points.

### Workers Observability

- Enable observability deliberately in config for production debugging.
- Use structured logs and avoid secret-bearing log fields.
- Tail logs are diagnostic, not durable audit storage.
- Use Workers observability for Worker telemetry; use Cloudflare Logs or Log Explorer for account/zone log datasets.

## Networking And Security

### Tunnel, Spectrum, And Private Connectivity

- Use Tunnel/Cloudflare One for private HTTP app exposure and Zero Trust access.
- Use Spectrum for non-HTTP TCP/UDP protocols.
- For private databases from Workers, compare Hyperdrive, TCP sockets, Tunnel, and VPC/private-network patterns against current docs.
- For physical/private interconnects, design high availability from the start; single-link setups are operationally fragile.

### WAF, API Shield, Bot Management

- Stage risky rules in log/simulate or narrow scopes before blocking production traffic.
- Schema and JWT validation failures are often configuration mismatch, not attacker traffic.
- API discovery and ML-driven features may need enough representative traffic before conclusions are reliable.
- Keep bypasses and exceptions documented with owner, reason, and expiry.

### Turnstile

- Prefer `turnstile-spin` for end-to-end setup.
- Never call siteverify directly from the browser; validate through a server-side Worker or backend.

## Deployment And IaC

### Terraform And Pulumi

- Do not manage the same Cloudflare resource from both Terraform/Pulumi and Wrangler unless the ownership boundary is explicit.
- IaC can create D1 databases, buckets, or projects, but app-level deploys and migrations may still require Wrangler or product-specific commands.
- Provider versions can rename resources or change schema. Retrieve current provider docs and migration guides before upgrading.
- Import existing resources before managing them declaratively.

### Pages, Workers Static Assets, And C3

- Confirm whether the project is Pages, Workers with static assets, or framework-generated before editing config.
- Bindings and environment variables differ between Pages Functions, Workers, and local dev.
- Do not start new Workers Sites projects; use Workers static assets or Pages.
- C3/scaffold commands change; retrieve current create command and platform flags before generating.

## Local Development

- Miniflare and local Wrangler simulations are fast feedback tools, not perfect production replicas.
- Remote bindings can touch real resources; call that out before enabling them.
- Some services require deployed or remote testing. Do not claim local-only validation proves production behavior.
