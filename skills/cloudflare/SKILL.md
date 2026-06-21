---
name: cloudflare
description: Route Cloudflare platform development tasks to current docs, MCP tools, and focused Cloudflare skills. Use for broad or ambiguous Cloudflare work across Workers, Pages, storage, AI, networking, security, analytics, media, or infrastructure-as-code; prefer narrower Cloudflare skills when the task clearly matches them.
---

# Cloudflare Platform

Use this skill as a routing and retrieval workflow for Cloudflare work. Do not treat it as a cached copy of the Cloudflare docs.

## Core Rule

Retrieve current Cloudflare information before relying on exact API fields, CLI flags, limits, pricing, compatibility dates, beta status, or configuration shapes.

Prefer sources in this order:

1. Cloudflare MCP/docs tools when available.
2. The relevant page under `https://developers.cloudflare.com/`.
3. Project-local source of truth: `wrangler.jsonc`, generated `worker-configuration.d.ts`, `node_modules/wrangler/config-schema.json`, package versions, tests, and deployed configuration inspected through Wrangler or the Cloudflare API.

When retrieved docs and local skill guidance disagree, trust the retrieved docs and generated/project-local types.

## Workflow

1. Classify the task: build, review, debug, migrate, configure, deploy, observe, or plan.
2. Prefer a focused skill when one matches: `wrangler`, `agents-sdk`, `durable-objects`, `workers-best-practices`, `sandbox-sdk`, `cloudflare-email-service`, `cloudflare-one`, `cloudflare-one-migrations`, `web-perf`, or `turnstile-spin`.
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
| Workers runtime, bindings, static assets, cron, tail Workers | Workers, `workers-best-practices`, `wrangler` | `https://developers.cloudflare.com/workers/` |
| CLI commands, deployment, resource creation, generated types | `wrangler` | `https://developers.cloudflare.com/workers/wrangler/` |
| Full-stack web apps and Git deploys | Pages or Workers static assets | `https://developers.cloudflare.com/pages/`, `https://developers.cloudflare.com/workers/static-assets/` |
| Stateful coordination, WebSockets, SQLite-per-object | `durable-objects` | `https://developers.cloudflare.com/durable-objects/` |
| Stateful AI agents, RPC, scheduling, chat, MCP, voice | `agents-sdk` | `https://developers.cloudflare.com/agents/` |
| Secure code execution and code interpreters | `sandbox-sdk` | `https://developers.cloudflare.com/sandbox/` |
| Transactional email sending or routing | `cloudflare-email-service` | `https://developers.cloudflare.com/email-service/` |
| Turnstile setup or CAPTCHA migration | `turnstile-spin` | `https://developers.cloudflare.com/turnstile/` |
| KV, D1, R2, Queues, Vectorize, Hyperdrive | Product docs plus `wrangler` | `https://developers.cloudflare.com/` |
| Workers AI model inference | Workers AI docs plus Workers bindings | `https://developers.cloudflare.com/workers-ai/` |
| AI provider routing, caching, logs, fallback | AI Gateway docs | `https://developers.cloudflare.com/ai-gateway/` |
| Managed RAG, semantic search, agent search endpoint | AI Search docs | `https://developers.cloudflare.com/ai-search/` |
| Tunnel, Magic WAN, WARP, Access, Gateway, DLP, CASB | `cloudflare-one` | `https://developers.cloudflare.com/cloudflare-one/` |
| WAF, DDoS, Bot Management, API Shield | Product docs and Cloudflare API schema | `https://developers.cloudflare.com/` |
| Terraform or Pulumi | Provider docs plus Cloudflare API schema | `https://developers.cloudflare.com/terraform/` |
| Worker logs, traces, metrics, Query Builder | Workers observability docs | `https://developers.cloudflare.com/workers/observability/` |
| Account/zone logs, forensic log search | Logs and Log Explorer docs | `https://developers.cloudflare.com/logs/`, `https://developers.cloudflare.com/log-explorer/` |
| GraphQL analytics, Web Analytics, custom Worker metrics | Analytics docs | `https://developers.cloudflare.com/analytics/` |

## Defaults

- Prefer `wrangler.jsonc` for new Workers configuration.
- Run `npx wrangler types` after binding or config changes in TypeScript projects.
- Use bindings from Workers code when available instead of calling the Cloudflare REST API from inside a Worker.
- Use `ctx.waitUntil()` for post-response work in Workers.
- Keep secrets out of source, config, command history, logs, and chat. Use Wrangler secrets or platform secret storage.
- For account-changing actions, identify the account/zone/project first and surface the target before making the change.
- For deploys, deletes, migrations, DNS/security changes, and other high-blast-radius operations, stage or dry-run first when the platform supports it.

## What Not To Add

This skill intentionally does not bundle local copies of Cloudflare product documentation. Add a local reference only when it captures durable agent-facing procedure, project-specific conventions, or non-obvious failure modes that are not already easy to retrieve from Cloudflare docs.

Do not add generated API references, command inventories, pricing tables, limits tables, or broad product docs mirrors. Link to current docs and retrieve them when needed.
