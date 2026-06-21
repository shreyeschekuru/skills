---
name: wrangler
description: Use Cloudflare Wrangler for Workers and Cloudflare resource workflows. Load before running or writing Wrangler commands, editing wrangler.jsonc, deploying Workers, temporary deploys, generating types, managing secrets, tailing logs, or creating KV, R2, D1, Queues, Vectorize, Workflows, Hyperdrive, Containers, Workers AI, Pages, Pipelines, Browser Run, AI Search, Workers VPC, mTLS, Artifacts, Agent Memory, or Secrets Store resources.
---

# Wrangler CLI

Use this skill for command selection, configuration edits, and validation around Wrangler. Retrieve current docs or `--help` output before relying on exact flags or subcommands.

## Sources

Prefer current sources in this order:

1. Project-local Wrangler: `npx wrangler --version`, `npx wrangler <command> --help`.
2. Wrangler docs: `https://developers.cloudflare.com/workers/wrangler/`.
3. Config schema: `node_modules/wrangler/config-schema.json`.
4. Generated types: `worker-configuration.d.ts` from `npx wrangler types`.

When docs, schema, and generated types disagree, trust the project-local Wrangler version for commands being run in that project.

## Workflow

1. Inspect the project: package manager, `wrangler.jsonc`/`wrangler.toml`, framework, scripts, `package.json`, generated types, and existing bindings.
2. Check Wrangler availability with `npx wrangler --version`. Add `wrangler` as a dev dependency when the project does not already manage it.
3. Retrieve the exact command shape with `npx wrangler <group> --help` or current docs before creating resources, changing config, or using a less-common flag.
4. Prefer `wrangler.jsonc` for new or migrated Workers config.
5. After config or binding changes, run `npx wrangler types`.
6. Validate deployable changes with `npx wrangler deploy --dry-run` when possible, plus the project test/typecheck command.
7. Report the account, worker/project name, environment, and validation commands used.

## Command Defaults

| Task | Default command or check |
| --- | --- |
| New Workers app | `npm create cloudflare@latest` or the repo's existing scaffold command |
| Local dev | `npx wrangler dev` |
| Deploy | `npx wrangler deploy` |
| Temporary deploy for unauthenticated prototypes | `npx wrangler deploy --temporary` only after Wrangler suggests it or the docs confirm support |
| Dry run deploy | `npx wrangler deploy --dry-run` |
| Generate types | `npx wrangler types` |
| Auth/account check | `npx wrangler whoami` |
| Tail logs | `npx wrangler tail` |
| Startup profiling | `npx wrangler check startup` |
| Secret write | `npx wrangler secret put NAME` |
| Bulk secret sync | `npx wrangler secret bulk < secrets.json` |
| Command discovery | `npx wrangler <group> --help` |

Use environment flags only after confirming the project defines the target environment.

## Configuration Rules

- Prefer `wrangler.jsonc` over TOML for new config.
- Keep `compatibility_date` current for new projects; avoid changing it casually in existing production projects.
- Add `nodejs_compat` only when the app or dependencies need Node.js built-ins.
- Do not hand-write Worker binding interfaces in TypeScript; regenerate types instead.
- Keep non-secret config in `vars`; keep secrets in Wrangler secrets or platform secret storage.
- Use `secrets.required` to declare required secrets when the project relies on deploy-time validation and generated types.
- Match binding names in config to code and generated types.
- Binding arrays are often non-inheritable across Wrangler environments. Repeat bindings such as `vars`, KV, D1, R2, AI Search, Vectorize, service bindings, Queues, Workflows, tail consumers, required secrets, and Secrets Store entries in each environment that needs them.
- For Durable Objects, never edit old migration tags; add a new migration tag.
- For D1 projects with nested migrations, use `migrations_pattern` after retrieving current D1 migration docs. The pattern is relative to the Wrangler config file and defaults to `${migrations_dir}/*.sql`.
- For Pipelines bindings, use the `stream` config field. The old `pipeline` field is deprecated, though the runtime binding API remains the same.
- Automatic resource provisioning can create KV, R2, and D1 resources when IDs are omitted, but generated IDs may be written back only for local CLI deploy flows. Inspect docs before relying on dashboard/Git deploys to update source config.
- Frameworks and build tools may redirect Wrangler to generated config under `.wrangler/deploy/config.json`; inspect generated config before diagnosing deploy/dev behavior.
- For local development with real remote resources, set per-binding `remote: true` only after the user understands it will touch real resources.

## Resource Workflows

Retrieve current command help before using these groups:

| Resource | Help entry point |
| --- | --- |
| KV | `npx wrangler kv --help` |
| R2 | `npx wrangler r2 --help` |
| D1 | `npx wrangler d1 --help` |
| Queues | `npx wrangler queues --help` |
| Vectorize | `npx wrangler vectorize --help` |
| Hyperdrive | `npx wrangler hyperdrive --help` |
| Workflows | `npx wrangler workflows --help` |
| Pipelines | `npx wrangler pipelines --help` |
| Browser Run sessions | `npx wrangler browser --help` |
| AI Search | `npx wrangler ai-search --help` |
| Workers VPC | `npx wrangler vpc --help` |
| mTLS certificates | `npx wrangler mtls-certificate --help` |
| Artifacts | `npx wrangler artifacts --help` |
| Agent Memory namespaces | `npx wrangler agent-memory --help` |
| Secrets Store | `npx wrangler secrets-store --help` |
| Pages | `npx wrangler pages --help` |
| Containers | Check current Containers docs and Wrangler help; commands and limits change quickly. |
| Dynamic Workers / Worker Loaders | Check current Dynamic Workers docs plus config schema; this is primarily `worker_loaders` config, not a resource command group. |

Prefer Wrangler-managed resource creation when the user wants local reproducibility or generated config. Prefer Dashboard/API inspection when the task is audit-only.

## Secrets

- Never pass secret values as command arguments.
- Prefer the interactive `npx wrangler secret put NAME` prompt.
- For automation, pipe from a protected file or secret manager; do not use `echo` in examples.
- For bulk updates, prefer JSON piped to `npx wrangler secret bulk`. A secret value creates/updates it, `null` deletes it, omitted secrets are unchanged, `.env` input cannot delete secrets, and current requests support up to 100 create/update/delete operations.
- Do not print existing secret values. Wrangler normally only lists names.
- Confirm the worker name and environment before writing or deleting a secret.

## Validation

Use the tightest validation set the project supports:

```bash
npx wrangler types
npx wrangler deploy --dry-run
npm test
npx tsc --noEmit
```

Skip unavailable commands with a short note. If validation fails because a command or flag is stale, retrieve current Wrangler help, adjust, and rerun.

## Gotchas

- `wrangler dev` uses local simulations by default for many bindings; remote resources require explicit remote configuration.
- Browser Run Quick Actions through the Worker binding require remote mode in local development and a current compatibility date.
- Workers AI and some platform services may require remote mode or deployed testing.
- `.dev.vars` is for local development; it is not deployed as production secrets.
- `--env` changes names, bindings, and variables according to the config environment. Inspect the config before using it.
- `wrangler deploy --temporary` is for unauthenticated agent/prototype deploys, not production or CI. Claim URLs are sensitive and expire if not used.
- `--temporary` is not a global flag and only supports a subset of products. Retrieve claim deployment docs before using advanced bindings.
- Deleting Workers, resources, namespaces, buckets, databases, or secrets is destructive. Confirm the exact target first.
- If a repo pins Wrangler, use the pinned version unless the task is explicitly to upgrade it.
