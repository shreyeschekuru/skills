---
description: Build a Cloudflare AI agent using @cloudflare/think — the higher-level chat agent class with automatic tool loops and built-in workspace
argument-hint: [agent-description]
allowed-tools: [Read, Glob, Grep, Bash, Write, Edit, WebFetch]
---

# Build a Think Agent on Cloudflare

## Arguments

The user invoked this command with: $ARGUMENTS

## Instructions

When this command is invoked:

1. Read `agents-sdk/references/think.md` — core Think API, hooks, sub-agents, comparison with AIChatAgent
2. Read `agents-sdk/references/configuration.md` — wrangler config, `experimental` flag requirement, Vite setup
3. Read `agents-sdk/references/routing.md` — `routeAgentRequest`, URL patterns, `getAgentByName`
4. Read `agents-sdk/references/client-sdk.md` — `useAgent`, `useAgentChat` React hooks
5. Based on what the user wants to build, also read:
   - Custom tools: `agents-sdk/references/streaming-chat.md` (tool patterns)
   - Background tasks: `agents-sdk/references/state-scheduling.md`
   - Approval gates: `agents-sdk/references/human-in-the-loop.md`
   - MCP integration: `agents-sdk/references/mcp.md`
   - Orchestration: `agents-sdk/references/codemode.md`
   - Sub-agents: re-read `think.md` `subAgent()` section
6. Fetch https://developers.cloudflare.com/agents/api-reference/think/ for the latest Think API

## Scaffold Steps

1. **Create project**
   ```bash
   npx create-cloudflare@latest my-think-agent --template cloudflare/agents-starter
   cd my-think-agent
   ```

2. **Install Think**
   ```bash
   npm install @cloudflare/think workers-ai-provider agents
   ```

3. **Configure `wrangler.jsonc`** — Think requires the `experimental` flag
   ```jsonc
   {
     "name": "my-think-agent",
     "main": "src/index.ts",
     "compatibility_date": "2025-01-28",
     "compatibility_flags": ["nodejs_compat", "experimental"],
     "durable_objects": {
       "bindings": [{ "name": "MyAgent", "class_name": "MyAgent" }]
     },
     "migrations": [{ "tag": "v1", "new_sqlite_classes": ["MyAgent"] }],
     "ai": { "binding": "AI" }
   }
   ```

4. **Implement the Think agent** in `src/index.ts`
   ```typescript
   import { Think } from "@cloudflare/think";
   import { createWorkersAI } from "workers-ai-provider";
   import { routeAgentRequest } from "agents";

   export class MyAgent extends Think<Env> {
     getModel() {
       return createWorkersAI({ binding: this.env.AI })(
         "@cf/meta/llama-4-scout-17b-16e-instruct"
       );
     }

     getSystemPrompt() {
       return "You are a helpful assistant.";
     }
   }

   export default {
     fetch: (req: Request, env: Env) =>
       routeAgentRequest(req, env) ?? new Response("Not found", { status: 404 })
   };
   ```

5. **Add custom tools** (if needed)
   ```typescript
   import { tool } from "ai";
   import { z } from "zod";

   export class MyAgent extends Think<Env> {
     getModel() { /* ... */ }
     getSystemPrompt() { return "You are a helpful assistant."; }

     getTools() {
       return {
         myTool: tool({
           description: "Does something useful",
           parameters: z.object({ input: z.string() }),
           execute: async ({ input }) => `Result: ${input}`
         })
       };
     }
   }
   ```

6. **Wire the React client** in `src/client.tsx`
   ```tsx
   import { useAgent } from "agents/react";
   import { useAgentChat } from "@cloudflare/ai-chat/react";

   export function Chat() {
     const agent = useAgent({ agent: "MyAgent", name: "session-1" });
     const { messages, input, handleInputChange, handleSubmit, status } =
       useAgentChat({ agent });

     return (
       <div>
         {messages.map((m) => (
           <div key={m.id}><strong>{m.role}:</strong> {m.content}</div>
         ))}
         <form onSubmit={handleSubmit}>
           <input
             value={input}
             onChange={handleInputChange}
             disabled={status === "streaming"}
             placeholder="Message..."
           />
           <button type="submit">Send</button>
         </form>
       </div>
     );
   }
   ```

7. **Develop locally**
   ```bash
   npx wrangler dev
   ```

8. **Deploy**
   ```bash
   npx wrangler deploy
   ```

## Key Decisions

### Think vs AIChatAgent vs Agent

| Need | Use |
|------|-----|
| Auto tool loop, built-in workspace, minimal boilerplate | `Think` (`@cloudflare/think`) |
| Full control over `streamText` loop and tool wiring | `AIChatAgent` (`@cloudflare/ai-chat`) |
| Custom stateful logic, RPC, scheduling — no chat needed | `Agent` (`agents`) |

### Think lifecycle hooks

Override these in your class to customize behavior:

| Hook | When to override |
|------|-----------------|
| `configureSession()` | Set up memory or context providers on first connect |
| `beforeTurn(ctx)` | Per-turn model/tools/system prompt swap; return `TurnConfig` |
| `onChunk(chunk)` | Progress tracking or streaming side-effects |
| `onChatResponse(result)` | Chaining turns, follow-up `saveMessages` calls |
| `onChatError(error)` | Custom error recovery |

### Sub-agents

```typescript
async beforeTurn(ctx) {
  if (needsSpecialist(ctx)) {
    const child = this.subAgent(SpecialistAgent, "specialist-1");
    await child.chat("Analyze this...", (chunk) => { /* stream */ });
  }
  return {};
}
```

### Switching models per-turn

```typescript
async beforeTurn(ctx) {
  if (ctx.continuation) {
    // cheaper model for follow-up turns
    return {
      model: createWorkersAI({ binding: this.env.AI })(
        "@cf/meta/llama-3.1-8b-instruct"
      )
    };
  }
  return {};
}
```

## Gotchas

- Think requires `"experimental"` in `compatibility_flags` — without it the agent will not start
- Do NOT enable `experimentalDecorators` in tsconfig — it breaks `@callable` if you mix in `Agent` patterns
- Every agent class needs its own DO binding AND a `new_sqlite_classes` migration entry
- Never edit old migration tags — add a new `v2` tag for schema changes
- For Workers AI locally, set `"ai": { "binding": "AI", "remote": true }` in your dev config

## Example Usage

```
/build-think-agent a customer support bot with escalation tools
/build-think-agent a research assistant that can browse the web
/build-think-agent a coding tutor with a built-in code execution sandbox
/build-think-agent an orchestrator that delegates to specialist sub-agents
```
