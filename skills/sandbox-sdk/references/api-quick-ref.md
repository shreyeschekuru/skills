# Sandbox SDK API Reference

## Contents
- Lifecycle
- Commands
- Code Interpreter
- Files
- Tunnels
- Error Handling


Detailed API for `@cloudflare/sandbox`. For full docs: https://developers.cloudflare.com/sandbox/api/

## Lifecycle

```typescript
getSandbox(binding: DurableObjectNamespace<Sandbox>, sandboxId: string, options?: SandboxOptions): Sandbox

interface SandboxOptions {
  sleepAfter?: string;     // Duration before auto-sleep (default: "10m")
  keepAlive?: boolean;     // Prevent auto-sleep (default: false)
  normalizeId?: boolean;   // Lowercase IDs for preview URLs (default: false)
  transport?: 'rpc';       // Prefer RPC transport for current SDKs
  enableDefaultSession?: boolean;  // Set false; default sessions are deprecated
}

await sandbox.destroy(): Promise<void>  // Immediately terminate and delete all state
```

Use `sandbox.createSession()` for deliberate shared shell state. Do not rely on the deprecated default `exec()` session behavior for generated command sequences.

## Commands

```typescript
await sandbox.exec(command: string, options?: ExecOptions): Promise<ExecResult>
await sandbox.startProcess(command: string, options?: ExecOptions): Promise<Process>

interface ExecOptions {
  cwd?: string;           // Working directory
  env?: Record<string, string>;  // Environment variables
  timeout?: number;       // Timeout in ms (no default; runs without timeout if unset)
  stdin?: string;         // Input to command
}

interface ExecResult {
  stdout: string;
  stderr: string;
  exitCode: number;
  success: boolean;       // exitCode === 0
}
```

## Code Interpreter

```typescript
await sandbox.createCodeContext(options?: CreateContextOptions): Promise<CodeContext>

interface CreateContextOptions {
  language?: 'python' | 'javascript' | 'typescript';  // default: 'python'
  cwd?: string;           // Working directory (default: '/workspace')
  envVars?: Record<string, string>;
  timeout?: number;       // Request timeout in ms (default: 30000)
}

await sandbox.runCode(code: string, options?: RunCodeOptions): Promise<ExecutionResult>

interface RunCodeOptions {
  context?: CodeContext;  // Reuse context for state persistence
  language?: 'python' | 'javascript' | 'typescript';
  timeout?: number;       // Execution timeout in ms (default: 60000)
}

interface ExecutionResult {
  code: string;
  logs: { stdout: string[]; stderr: string[] };
  results: RichOutput[];  // text, html, png, json, etc.
  error?: { name: string; value: string; traceback: string[] };
  executionCount: number;
}
```

## Files

```typescript
await sandbox.writeFile(path: string, content: string | Uint8Array): Promise<void>
await sandbox.readFile(path: string): Promise<{ content: string }>
await sandbox.mkdir(path: string, options?: { recursive?: boolean }): Promise<void>
await sandbox.listFiles(path: string): Promise<FileMetadata[]>
await sandbox.deleteFile(path: string): Promise<void>

interface FileMetadata {
  name: string;
  path: string;
  isDirectory: boolean;
  size: number;
  modifiedAt: string;
}
```

## Tunnels

```typescript
await sandbox.tunnels.get(port: number, options?: { name?: string }): Promise<{ url: string }>
```

Quick tunnels create zero-config `*.trycloudflare.com` URLs. Named tunnels create persistent hostnames in the account's zone. `exposePort()` and `unexposePort()` are deprecated; do not use them in new code.

## Error Handling

Errors include context about the operation:

```typescript
try {
  await sandbox.exec('invalid-command');
} catch (error) {
  // error.message includes command and sandbox context
}
```

For `runCode()`, check `result.error` instead of catching:

```typescript
const result = await sandbox.runCode('1/0', { language: 'python' });
if (result.error) {
  console.error(result.error.name);  // "ZeroDivisionError"
}
```
