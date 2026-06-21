# Cloudflare Skills

A collection of [Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) for building on Cloudflare, Workers, the Agents SDK, and the wider Cloudflare Developer Platform.

## Installing

These skills work with any agent that supports the Agent Skills standard, including Claude Code, OpenCode, OpenAI Codex, and Pi.

### Claude Code

Install using the [plugin marketplace](https://code.claude.com/docs/en/discover-plugins#add-from-github):

```
/plugin marketplace add cloudflare/skills
/plugin install cloudflare@cloudflare
```

### Cursor

Install from the Cursor Marketplace or add manually via **Settings > Rules > Add Rule > Remote Rule (Github)** with `cloudflare/skills`.

### npx skills

Install using the [`npx skills`](https://www.skills.sh/) CLI:

```
npx skills add https://github.com/cloudflare/skills
```

### Clone / Copy

Clone this repo and copy the skill folders into the appropriate directory for your agent:

| Agent | Skill Directory | Docs |
|-------|-----------------|------|
| Claude Code | `~/.claude/skills/` | [docs](https://code.claude.com/docs/en/skills) |
| Cursor | `~/.cursor/skills/` | [docs](https://cursor.com/docs/skills) |
| OpenCode | `~/.config/opencode/skills/` | [docs](https://opencode.ai/docs/skills/) |
| OpenAI Codex | `~/.codex/skills/` | [docs](https://developers.openai.com/codex/skills) |
| Pi | `~/.pi/agent/skills/` | [docs](https://github.com/earendil-works/pi/tree/main/packages/coding-agent#skills) |

## Commands

Commands are user-invocable slash commands that you explicitly call.

| Command | Description |
|---------|-------------|
| `/cloudflare:build-agent` | Build an AI agent on Cloudflare using the Agents SDK |
| `/cloudflare:build-mcp` | Build an MCP server on Cloudflare |

## Skills

Skills are contextual and auto-loaded based on your conversation. When a request matches a skill's triggers, the agent loads and applies the relevant skill to provide accurate, up-to-date guidance.

| Skill | Useful for |
|-------|------------|
| cloudflare | Retrieval-first platform router for broad Cloudflare tasks, product selection, current docs, MCP tools, and focused skills |
| agents-sdk | Building stateful AI agents with state, scheduling, RPC, MCP servers, email, and streaming chat |
| browser-run | Browser automation on Cloudflare with screenshots, PDFs, crawlers, CDP, Playwright, Puppeteer, Live View, and WebMCP |
| cloudflare-email-service | Transactional email sending, Email Routing, Workers email bindings, REST email API, and deliverability |
| dynamic-workers | Runtime-loaded Workers, Worker Loader bindings, generated-code execution, egress control, and dynamic sandboxing |
| durable-objects | Stateful coordination (chat rooms, games, booking), RPC, SQLite, alarms, WebSockets |
| sandbox-sdk | Secure code execution for AI code execution, code interpreters, CI/CD systems, and interactive dev environments |
| workflows | Durable multi-step Workers applications with retries, sleeps, events, rollback, and instance management |
| wrangler | Deploying and managing Workers, KV, R2, D1, Vectorize, Queues, Workflows, Browser Run, AI Search, and related resources |
| web-perf | Auditing Core Web Vitals (FCP, LCP, TBT, CLS), render-blocking resources, network chains |
| workers-best-practices | Reviewing and authoring production Workers code, bindings, generated types, streaming, secrets, and observability |
| turnstile-spin | End-to-end Turnstile setup, CAPTCHA migration, managed siteverify Worker deployment, snippets, and validation |

## Cloudflare One

Short, retrieval-first skills for [Cloudflare One](https://developers.cloudflare.com/cloudflare-one/) work.

| Skill | Useful for |
|-------|------------|
| cloudflare-one | Designing, configuring, troubleshooting, or reviewing Cloudflare One deployments across Access, Gateway, WARP, Tunnel, Magic WAN, DLP, CASB, posture, and identity |
| cloudflare-one-migrations | Migration assessments, policy mapping, rollout plans, and gap analysis for Zscaler, Palo Alto, legacy VPN/SWG, and SASE migrations to Cloudflare One |

## MCP Servers

This plugin includes [Cloudflare's remote MCP servers](https://developers.cloudflare.com/agents/model-context-protocol/cloudflare/servers-for-cloudflare/) for enhanced functionality:

| Server | Purpose |
|--------|---------|
| cloudflare-api | Manage Cloudflare account resources, zones, and settings |
| cloudflare-docs | Up-to-date Cloudflare documentation and reference |
| cloudflare-bindings | Build Workers applications with storage, AI, and compute primitives |
| cloudflare-builds | Manage and get insights into Workers builds |
| cloudflare-observability | Debug and analyze application logs and analytics |

## Resources

- [Cloudflare Agents Documentation](https://developers.cloudflare.com/agents/)
- [Cloudflare MCP Guide](https://developers.cloudflare.com/agents/model-context-protocol/)
- [Agents SDK Repository](https://github.com/cloudflare/agents)
- [Agents Starter Template](https://github.com/cloudflare/agents-starter)
