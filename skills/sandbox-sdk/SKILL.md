---
name: sandbox-sdk
description: Build sandboxed applications for secure code execution on Cloudflare. Load for AI code execution, code interpreters, CI/CD sandboxes, interactive dev environments, untrusted code, Sandbox SDK lifecycle, commands, files, preview URLs, or Dockerfile configuration. Retrieve current docs before exact APIs.
---

# Cloudflare Sandbox SDK

Build secure, isolated code execution environments on Cloudflare Workers.

## FIRST: Verify Installation

```bash
npm install @cloudflare/sandbox
docker info  # Must succeed - Docker required for local dev
```

## Retrieval Sources

Your knowledge of the Sandbox SDK may be outdated. **Prefer retrieval over pre-training** for any Sandbox SDK task.

| Resource | URL | Use for |
|----------|-----|---------|
| Overview | https://developers.cloudflare.com/sandbox/ | Product fit, architecture, feature map |
| Get Started | https://developers.cloudflare.com/sandbox/get-started/ | First Worker setup |
| API Reference | https://developers.cloudflare.com/sandbox/api/ | Current method signatures and options |
| Examples | https://github.com/cloudflare/sandbox-sdk/tree/main/examples | Complete runnable patterns |

When implementing features, fetch the relevant docs page or example first.

### Retrieval Map

| Need | Fetch |
|------|-------|
| Commands, background processes, streaming output | `https://developers.cloudflare.com/sandbox/api/commands/`, `https://developers.cloudflare.com/sandbox/guides/execute-commands/`, `https://developers.cloudflare.com/sandbox/guides/background-processes/`, `https://developers.cloudflare.com/sandbox/guides/streaming-output/` |
| File operations, file watching, project workspaces | `https://developers.cloudflare.com/sandbox/api/files/`, `https://developers.cloudflare.com/sandbox/guides/manage-files/`, `https://developers.cloudflare.com/sandbox/api/file-watching/` |
| Code interpreter and AI-generated code | `https://developers.cloudflare.com/sandbox/api/interpreter/`, `https://developers.cloudflare.com/sandbox/guides/code-execution/` |
| Preview URLs, tunnels, exposed services, production domains | `https://developers.cloudflare.com/sandbox/api/tunnels/`, `https://developers.cloudflare.com/sandbox/api/ports/`, `https://developers.cloudflare.com/sandbox/concepts/preview-urls/`, `https://developers.cloudflare.com/sandbox/guides/expose-services/`, `https://developers.cloudflare.com/sandbox/guides/production-deployment/` |
| Sessions, browser terminals, WebSockets | `https://developers.cloudflare.com/sandbox/api/sessions/`, `https://developers.cloudflare.com/sandbox/concepts/sessions/`, `https://developers.cloudflare.com/sandbox/api/terminal/`, `https://developers.cloudflare.com/sandbox/guides/browser-terminals/`, `https://developers.cloudflare.com/sandbox/guides/websocket-connections/` |
| Wrangler config, Dockerfiles, options, transports | `https://developers.cloudflare.com/sandbox/configuration/wrangler/`, `https://developers.cloudflare.com/sandbox/configuration/dockerfile/`, `https://developers.cloudflare.com/sandbox/configuration/sandbox-options/`, `https://developers.cloudflare.com/sandbox/configuration/transport/` |
| Persistent storage, R2 mounts, Workers binding access, outbound API proxying | `https://developers.cloudflare.com/sandbox/api/storage/`, `https://developers.cloudflare.com/sandbox/guides/mount-buckets/`, `https://developers.cloudflare.com/sandbox/guides/workers-connections/`, `https://developers.cloudflare.com/sandbox/guides/outbound-traffic/` |
| Lifecycle, security, limits, pricing, deprecations | `https://developers.cloudflare.com/sandbox/concepts/sandboxes/`, `https://developers.cloudflare.com/sandbox/concepts/security/`, `https://developers.cloudflare.com/sandbox/platform/limits/`, `https://developers.cloudflare.com/sandbox/platform/pricing/`, `https://developers.cloudflare.com/sandbox/guides/2026-deprecation/` |

## Required Configuration

**wrangler.jsonc** (exact - do not modify structure):

```jsonc
{
  "containers": [{
    "class_name": "Sandbox",
    "image": "./Dockerfile",
    "instance_type": "lite",
    "max_instances": 1
  }],
  "durable_objects": {
    "bindings": [{ "class_name": "Sandbox", "name": "Sandbox" }]
  },
  "migrations": [{ "new_sqlite_classes": ["Sandbox"], "tag": "v1" }]
}
```

**Worker entry** - must re-export Sandbox class:

```typescript
import { getSandbox } from '@cloudflare/sandbox';
export { Sandbox } from '@cloudflare/sandbox';  // Required export
```

## Quick Reference

| Task | Method |
|------|--------|
| Get sandbox | `getSandbox(env.Sandbox, 'user-123')` |
| Run command | `await sandbox.exec('python script.py')` |
| Start service | `await sandbox.startProcess('python -m http.server 8080')` |
| Run code (interpreter) | `await sandbox.runCode(code, { language: 'python' })` |
| Write file | `await sandbox.writeFile('/workspace/app.py', content)` |
| Read file | `await sandbox.readFile('/workspace/app.py')` |
| Create directory | `await sandbox.mkdir('/workspace/src', { recursive: true })` |
| List files | `await sandbox.listFiles('/workspace')` |
| Preview URL | `await sandbox.tunnels.get(8080)` |
| Destroy | `await sandbox.destroy()` |

## Core Patterns

### Execute Commands

```typescript
const sandbox = getSandbox(env.Sandbox, 'user-123', {
  transport: 'rpc',
  enableDefaultSession: false
});
const result = await sandbox.exec('python --version');
// result: { stdout, stderr, exitCode, success }
```

Use `enableDefaultSession: false` for agent-generated command sequences unless the app deliberately creates explicit sessions. HTTP and WebSocket transports are deprecated; prefer RPC transport or the current docs' default.

### Code Interpreter (Recommended for AI)

Use `runCode()` for executing LLM-generated code with rich outputs:

```typescript
const ctx = await sandbox.createCodeContext({ language: 'python' });

await sandbox.runCode('import pandas as pd; data = [1,2,3]', { context: ctx });
const result = await sandbox.runCode('sum(data)', { context: ctx });
// result.results[0].text = "6"
```

**Languages**: `python`, `javascript`, `typescript`

State persists within context. Create explicit contexts for production.

### File Operations

```typescript
await sandbox.mkdir('/workspace/project', { recursive: true });
await sandbox.writeFile('/workspace/project/main.py', code);
const file = await sandbox.readFile('/workspace/project/main.py');
const files = await sandbox.listFiles('/workspace/project');
```

## API Choices

| Need | Use | Why |
|------|-----|-----|
| Shell commands, scripts | `exec()` | Direct control, streaming |
| LLM-generated code | `runCode()` | Rich outputs, state persistence |
| Build/test pipelines | `exec()` | Exit codes, stderr capture |
| Data analysis | `runCode()` | Charts, tables, pandas |

## Extending the Dockerfile

Base image (`docker.io/cloudflare/sandbox:0.7.0`) includes Python 3.11, Node.js 20, and common tools.

Add dependencies by extending the Dockerfile:

```dockerfile
FROM docker.io/cloudflare/sandbox:0.7.0

# Python packages
RUN pip install requests beautifulsoup4

# Node packages (global)
RUN npm install -g typescript

# System packages
RUN apt-get update && apt-get install -y ffmpeg && rm -rf /var/lib/apt/lists/*

EXPOSE 8080  # Optional documentation for services that listen on this port
```

Keep images lean - affects cold start time.

## Preview URLs (Tunnels)

Expose HTTP services running in sandboxes through Cloudflare Tunnel:

```typescript
await sandbox.startProcess('python -m http.server 8080');
const tunnel = await sandbox.tunnels.get(8080);
// tunnel.url is a zero-config *.trycloudflare.com URL
```

For stable URLs, use named tunnels:

```typescript
const tunnel = await sandbox.tunnels.get(8080, { name: 'my-app-preview' });
```

`exposePort()` is deprecated. Use `sandbox.tunnels` for new code; quick tunnels work on `.workers.dev`, and named tunnels create persistent hostnames in your zone. `sandbox.destroy()` tears down associated tunnels and DNS records.

See: https://developers.cloudflare.com/sandbox/guides/expose-services/

## OpenAI Agents SDK Integration

The SDK provides helpers for OpenAI Agents at `@cloudflare/sandbox/openai`:

```typescript
import { Shell, Editor } from '@cloudflare/sandbox/openai';
```

See `examples/openai-agents` for complete integration pattern.

## Sandbox Lifecycle

- `getSandbox()` returns immediately - container starts lazily on first operation
- Containers sleep after 10 minutes of inactivity (configurable via `sleepAfter`)
- Use `destroy()` to immediately free resources
- Same `sandboxId` always returns same sandbox instance
- Prefer explicit sessions with `sandbox.createSession()` when commands need shared shell state; default sessions are deprecated.

## Anti-Patterns

- **Don't use internal clients** (`CommandClient`, `FileClient`) - use `sandbox.*` methods
- **Don't skip the Sandbox export** - Worker won't deploy without `export { Sandbox }`
- **Don't use deprecated transports or `exposePort()`** - prefer RPC transport and `sandbox.tunnels`
- **Don't rely on default command sessions** - disable default sessions and create explicit sessions when needed
- **Don't hardcode sandbox IDs for multi-user** - use user/session identifiers
- **Don't forget cleanup** - call `destroy()` for temporary sandboxes

## Detailed References

- **[references/api-quick-ref.md](references/api-quick-ref.md)** - Full API with options and return types
- **[references/examples.md](references/examples.md)** - Example index with use cases
