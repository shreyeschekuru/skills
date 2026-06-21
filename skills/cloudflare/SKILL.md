---
name: cloudflare
description: Route Cloudflare platform development tasks to current docs, MCP tools, and focused Cloudflare skills. Use for broad or ambiguous Cloudflare work across Workers, Pages, storage, AI, browser automation, dynamic Workers, Workflows, zone/core configuration, SaaS platforms, networking, security, analytics, media, feature flags, bindings, or infrastructure-as-code; prefer narrower Cloudflare skills when the task clearly matches them.
---

# Cloudflare Platform

Use this skill as a routing and retrieval workflow for Cloudflare work. Do not treat it as a cached copy of the Cloudflare docs.

## Core Rule

Retrieve current Cloudflare information before relying on exact API fields, CLI flags, limits, pricing, compatibility dates, beta status, or configuration shapes.

Prefer sources in this order:

1. Cloudflare MCP/docs tools when available.
2. Cloudflare docs indexes for discovery only: `https://developers.cloudflare.com/llms.txt` or the relevant product `llms.txt`.
3. The relevant product documentation page under `https://developers.cloudflare.com/`. Prefer Markdown docs (`index.md` or `Accept: text/markdown`) over HTML when fetching directly.
4. Project-local source of truth: `wrangler.jsonc`, generated `worker-configuration.d.ts`, `node_modules/wrangler/config-schema.json`, package versions, tests, and deployed configuration inspected through Wrangler or the Cloudflare API.
5. Last resort: the Cloudflare Workers SDK repository (`https://github.com/cloudflare/workers-sdk`) for reverse-engineering Wrangler or Miniflare behavior when the sources above do not answer the question.

When retrieved docs and local skill guidance disagree, trust the retrieved docs and generated/project-local types.

## Workflow

1. Classify the task: build, review, debug, migrate, configure, deploy, observe, or plan.
2. Prefer a focused skill when one matches: `wrangler`, `browser-run`, `dynamic-workers`, `workflows`, `agents-sdk`, `durable-objects`, `workers-best-practices`, `sandbox-sdk`, `cloudflare-email-service`, `flagship`, `cloudflare-one`, `cloudflare-one-migrations`, `web-perf`, or `turnstile-spin`.
3. For broad product choice, read `references/product-selection.md`.
4. For products without a focused skill, or when debugging an implementation, read `references/platform-gotchas.md`.
5. Retrieve only the current docs needed for the products involved.
6. Inspect the project before changing it: package manager, framework, `wrangler.*`, generated types, bindings, compatibility date/flags, CI, tests, and deployment conventions.
7. Make the smallest change that satisfies the user request.
8. Validate with the closest project command: `npx wrangler types`, `npx wrangler deploy --dry-run`, `npm test`, `npx tsc --noEmit`, `npx vitest`, or product-specific validation.
9. Report exact docs or local files used when the answer depends on current behavior.

## Product Routing

| Need | Start with | Current docs |
| --- | --- | --- |
| Broad architecture or product fit across several Cloudflare services | Reference Architecture docs plus product docs | `https://developers.cloudflare.com/reference-architecture/` |
| Workers runtime, bindings, static assets, cron, tail Workers | Workers, `workers-best-practices`, `wrangler` | `https://developers.cloudflare.com/workers/` |
| CLI commands, deployment, resource creation, generated types | `wrangler` | `https://developers.cloudflare.com/workers/wrangler/` |
| Real browser automation, screenshots, PDFs, crawlers, CDP, Playwright, Puppeteer, WebMCP | `browser-run` | `https://developers.cloudflare.com/browser-run/` |
| Runtime-loaded or AI-generated Worker code, Worker Loaders, egress-controlled generated code | `dynamic-workers` | `https://developers.cloudflare.com/dynamic-workers/` |
| Durable multi-step orchestration, sleeps, retries, waitForEvent, rollbacks | `workflows` | `https://developers.cloudflare.com/workflows/` |
| Full-stack web apps and Git deploys | Pages or Workers static assets | `https://developers.cloudflare.com/pages/`, `https://developers.cloudflare.com/workers/static-assets/` |
| Stateful coordination, WebSockets, SQLite-per-object | `durable-objects` | `https://developers.cloudflare.com/durable-objects/` |
| Stateful AI agents, RPC, scheduling, chat, MCP, voice | `agents-sdk` | `https://developers.cloudflare.com/agents/` |
| Secure code execution and code interpreters | `sandbox-sdk` | `https://developers.cloudflare.com/sandbox/` |
| Transactional email sending or routing | `cloudflare-email-service` | `https://developers.cloudflare.com/email-service/` |
| Turnstile setup or CAPTCHA migration | `turnstile-spin` | `https://developers.cloudflare.com/turnstile/` |
| Feature flags, targeting, gradual rollouts | `flagship` | `https://developers.cloudflare.com/flagship/` |
| Zone onboarding, DNS records, nameservers, DNSSEC, internal DNS, private origins | DNS docs plus Cloudflare API/Terraform docs | `https://developers.cloudflare.com/dns/` |
| TLS certificates, modes, mTLS, keyless SSL, custom hostname certificates | SSL/TLS docs plus product docs for SaaS or API Shield | `https://developers.cloudflare.com/ssl/` |
| Redirects, URL/header transforms, origin rules, cache rules, compression rules, custom errors, request tracing | Rules and Ruleset Engine docs | `https://developers.cloudflare.com/rules/`, `https://developers.cloudflare.com/ruleset-engine/` |
| CDN cache behavior, cache rules, cache keys, cache security, Cache Reserve | Cache docs plus `platform-gotchas.md` | `https://developers.cloudflare.com/cache/` |
| Origin availability, health monitoring, global/local traffic steering, failover | Load Balancing, Health Checks, and Reference Architecture docs | `https://developers.cloudflare.com/load-balancing/`, `https://developers.cloudflare.com/health-checks/`, `https://developers.cloudflare.com/reference-architecture/architectures/load-balancing/` |
| Planned traffic surges, queues for visitors, event-based capacity control | Waiting Room docs | `https://developers.cloudflare.com/waiting-room/` |
| Versioned/staged zone configuration changes and rollbacks | Version Management docs | `https://developers.cloudflare.com/version-management/` |
| Resource organization, tags, ownership, billing attribution | Resource Tagging docs plus API/Terraform docs | `https://developers.cloudflare.com/resource-tagging/` |
| Multi-tenant SaaS platforms, customer custom hostnames, customer-uploaded code, tenant provisioning, OAuth integrations | Cloudflare for Platforms, Tenant, OAuth, and Reference Architecture docs | `https://developers.cloudflare.com/cloudflare-for-platforms/`, `https://developers.cloudflare.com/tenant/`, `https://developers.cloudflare.com/fundamentals/oauth/`, `https://developers.cloudflare.com/reference-architecture/design-guides/leveraging-cloudflare-for-your-saas-applications/` |
| KV, D1, R2, Queues, Vectorize, Hyperdrive | Product docs plus `wrangler` | `https://developers.cloudflare.com/` |
| R2 Data Catalog, R2 SQL, Pipelines, lakehouse analytics | Product docs plus `platform-gotchas.md` | `https://developers.cloudflare.com/r2-sql/`, `https://developers.cloudflare.com/pipelines/`, `https://developers.cloudflare.com/r2/data-catalog/` |
| Artifacts and Agent Memory | Product docs plus `wrangler` and `platform-gotchas.md` | `https://developers.cloudflare.com/artifacts/`, `https://developers.cloudflare.com/agent-memory/` |
| Images, Stream, Media Transformations | Product docs plus `platform-gotchas.md` | `https://developers.cloudflare.com/images/`, `https://developers.cloudflare.com/stream/`, `https://developers.cloudflare.com/stream/transform-videos/` |
| Rate Limiting, mTLS, Version Metadata, Secrets Store | Workers binding docs plus `wrangler` and `platform-gotchas.md` | `https://developers.cloudflare.com/workers/runtime-apis/bindings/rate-limit/`, `https://developers.cloudflare.com/workers/runtime-apis/bindings/mtls/`, `https://developers.cloudflare.com/workers/runtime-apis/bindings/version-metadata/`, `https://developers.cloudflare.com/secrets-store/` |
| Workers AI model inference | Workers AI docs plus Workers bindings | `https://developers.cloudflare.com/workers-ai/` |
| AI provider routing, caching, logs, fallback | AI Gateway docs | `https://developers.cloudflare.com/ai-gateway/` |
| Managed RAG, semantic search, agent search endpoint | AI Search docs | `https://developers.cloudflare.com/ai-search/` |
| Tunnel, Magic WAN, WARP, Access, Gateway, DLP, CASB | `cloudflare-one` | `https://developers.cloudflare.com/cloudflare-one/` |
| Realtime audio/video, SFU, TURN, RealtimeKit | Realtime docs plus `platform-gotchas.md` | `https://developers.cloudflare.com/realtime/` |
| Private Worker access to internal HTTP/TCP services | Workers VPC docs plus product chooser | `https://developers.cloudflare.com/workers-vpc/` |
| WAF managed/custom/rate limiting rules, account-level rules, exceptions, false positive tuning | WAF, Ruleset Engine, and WAF reference architecture docs | `https://developers.cloudflare.com/waf/`, `https://developers.cloudflare.com/ruleset-engine/`, `https://developers.cloudflare.com/reference-architecture/design-guides/streamlined-waf-deployment-across-zones-and-applications/` |
| Bot traffic, scraping, AI crawlers, bot scores, signed/verified bots | Bots docs plus WAF custom rules where enforcement uses rules | `https://developers.cloudflare.com/bots/`, `https://developers.cloudflare.com/waf/` |
| API discovery, schema/JWT/mTLS validation, sequence abuse, API routing | API Shield docs | `https://developers.cloudflare.com/api-shield/` |
| Client-side script/resource security, CSP monitoring, malicious script detection | Client-side security docs | `https://developers.cloudflare.com/client-side-security/` |
| Challenge pages, JavaScript detections, clearance cookies, challenge loops | Challenges docs plus Turnstile docs for embedded verification | `https://developers.cloudflare.com/cloudflare-challenges/`, `https://developers.cloudflare.com/turnstile/` |
| Terraform or Pulumi | Provider docs plus Cloudflare API schema | `https://developers.cloudflare.com/terraform/` |
| Worker logs, traces, metrics, Query Builder | Workers observability docs | `https://developers.cloudflare.com/workers/observability/` |
| Account/zone logs, forensic log search | Logs and Log Explorer docs | `https://developers.cloudflare.com/logs/`, `https://developers.cloudflare.com/log-explorer/` |
| GraphQL analytics, Web Analytics, custom Worker metrics | Analytics docs | `https://developers.cloudflare.com/analytics/` |

## Defaults

- Prefer `wrangler.jsonc` for new Workers configuration.
- Run `npx wrangler types` after binding or config changes in TypeScript projects.
- Use bindings from Workers code when available instead of calling the Cloudflare REST API from inside a Worker.
- Check the current Workers bindings docs before assuming a product has no Worker-native binding.
- Use `ctx.waitUntil()` for post-response work in Workers.
- Keep secrets out of source, config, command history, logs, and chat. Use Wrangler secrets or platform secret storage.
- For account-changing actions, identify the account/zone/project first and surface the target before making the change.
- For deploys, deletes, migrations, DNS/security changes, and other high-blast-radius operations, stage or dry-run first when the platform supports it.

## What Not To Add

This skill intentionally does not bundle local copies of Cloudflare product documentation. Add a local reference only when it captures durable agent-facing procedure, project-specific conventions, or non-obvious failure modes that are not already easy to retrieve from Cloudflare docs.

Do not add generated API references, command inventories, pricing tables, limits tables, or broad product docs mirrors. Link to current docs and retrieve them when needed.
