# Browse the Web With Browser Run

Fetch https://developers.cloudflare.com/agents/tools/browser/ and https://developers.cloudflare.com/browser-run/ for complete documentation.

CDP-powered browser tools let agents use Cloudflare Browser Run to scrape, screenshot, inspect, debug, and interact with rendered web pages.

## Setup

```jsonc
// wrangler.jsonc
{
  "browser": { "binding": "BROWSER" },
  "worker_loaders": [{ "binding": "LOADER" }],
  "compatibility_flags": ["nodejs_compat"]
}
```

Load `browser-run` for Browser Run-specific setup, Quick Actions, Playwright/Puppeteer/CDP sessions, Live View, WebMCP, local remote mode, limits, and pricing.

## Usage with AI SDK

```typescript
import { createBrowserTools } from "agents/browser/ai";

export class MyAgent extends AIChatAgent<Env> {
  async onChatMessage(onFinish) {
    const browserTools = createBrowserTools({
      browser: this.env.BROWSER,
      loader: this.env.LOADER
    });

    const result = streamText({
      model: openai("gpt-4o"),
      messages: await convertToModelMessages(this.messages),
      tools: { ...myTools, ...browserTools },
      onFinish
    });
    return result.toUIMessageStreamResponse();
  }
}
```

## Available Tools

| Tool | Purpose |
|------|---------|
| `browser_search` | Search the web and return results |
| `browser_execute` | Run durable browser automation code against a Browser Run session |

The LLM writes async JavaScript that can navigate, execute CDP operations, inspect pages, capture screenshots, read rendered content, and interact with sessions.

## Fit

- Need a real browser (JS rendering, screenshots, interaction) → browser tools
- Just need HTML/API data → use `fetch()` instead (faster, cheaper)
- Need browser integration details outside Agents SDK wiring → load `browser-run`
- Need generated code execution or Worker Loader details → load `dynamic-workers`

## Browser Run Notes

- Browser Run was formerly Browser Rendering; docs and examples may mix the names.
- Treat Live View URLs, CDP endpoints, browser session IDs, screenshots, and recordings as sensitive.
- Use human-in-the-loop or Live View for MFA and sensitive confirmations.
- If a site exposes WebMCP tools, prefer structured WebMCP calls before click/type automation. WebMCP currently requires lab sessions; retrieve current limits before relying on it.
- Browser Run Quick Actions are a separate Browser Run integration path. Use them for one-shot screenshots, PDFs, Markdown, HTML, links, snapshots, and crawls.

## Low-Level API

```typescript
import { connectBrowser, CdpSession } from "agents/browser";

const browser = await connectBrowser(this.env.BROWSER);
const cdp = new CdpSession(browser);
await cdp.send("Page.navigate", { url: "https://example.com" });
```
