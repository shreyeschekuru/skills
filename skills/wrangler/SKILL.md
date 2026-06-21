---
name: wrangler
description: Use Cloudflare Wrangler for Workers and Cloudflare resource workflows. Load before running or writing Wrangler commands, editing wrangler.jsonc, deploying Workers, generating types, managing secrets, tailing logs, or creating KV, R2, D1, Queues, Vectorize, Workflows, Hyperdrive, Containers, Workers AI, Pages, Pipelines, or Secrets Store resources.
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
| Dry run deploy | `npx wrangler deploy --dry-run` |
| Generate types | `npx wrangler types` |
| Auth/account check | `npx wrangler whoami` |
| Tail logs | `npx wrangler tail` |
| Startup profiling | `npx wrangler check startup` |
| Secret write | `npx wrangler secret put NAME` |
| Command discovery | `npx wrangler <group> --help` |

Use environment flags only after confirming the project defines the target environment.

## Configuration Rules

- Prefer `wrangler.jsonc` over TOML for new config.
- Keep `compatibility_date` current for new projects; avoid changing it casually in existing production projects.
- Add `nodejs_compat` only when the app or dependencies need Node.js built-ins.
- Do not hand-write Worker binding interfaces in TypeScript; regenerate types instead.
- Keep non-secret config in `vars`; keep secrets in Wrangler secrets or platform secret storage.
- Match binding names in config to code and generated types.
- For Durable Objects, never edit old migration tags; add a new migration tag.
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
| Secrets Store | `npx wrangler secrets-store --help` |
| Pages | `npx wrangler pages --help` |
| Containers | Check current Containers docs and Wrangler help; commands and limits change quickly. |

Prefer Wrangler-managed resource creation when the user wants local reproducibility or generated config. Prefer Dashboard/API inspection when the task is audit-only.

## Secrets

- Never pass secret values as command arguments.
- Prefer the interactive `npx wrangler secret put NAME` prompt.
- For automation, pipe from a protected file or secret manager; do not use `echo` in examples.
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
- Workers AI and some platform services may require remote mode or deployed testing.
- `.dev.vars` is for local development; it is not deployed as production secrets.
- `--env` changes names, bindings, and variables according to the config environment. Inspect the config before using it.
- Deleting Workers, resources, namespaces, buckets, databases, or secrets is destructive. Confirm the exact target first.
- If a repo pins Wrangler, use the pinned version unless the task is explicitly to upgrade it.
