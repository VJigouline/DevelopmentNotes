# Install & run a Model Context Protocol (MCP) server for Angular (VS Code)

This document explains how to run a simple local Model Context Protocol (MCP) server and connect it to VS Code tooling for Angular. It also includes a small, copy-ready Node.js example server that implements basic MCP endpoints so you can test integration locally.

Note: "MCP" is a protocol surface that tools (editors, agents) use to request contextual information (files, diagnostics, AST, workspace metadata) from a language/project-specific server. The exact endpoint names and payloads depend on the MCP consumer. This guide gives a practical, minimal server you can adapt for Angular contexts and a recommended VS Code configuration pattern.

## Goals
- Provide a minimal, runnable MCP server skeleton (Node.js/Express).
- Show how to run it locally on Windows PowerShell.
- Show how to configure a VS Code MCP-capable client (generic settings) to connect.
- Provide troubleshooting and security notes.

---

## Prerequisites
- Node.js (LTS) and `npm` installed. Verify:

```powershell
node --version
npm --version
```

- VS Code (latest) installed.
- An MCP-aware VS Code extension or client configured to talk to your MCP server. If you're using a specific extension that expects MCP, follow its docs then point it to the server URL below.

---

## 1) Scaffold a minimal MCP server (Node.js)

Create a folder for the server and scaffold a minimal project.

```powershell
mkdir angular-mcp-server; cd angular-mcp-server
npm init -y
npm install express cors body-parser
```

Create a file `server.js` with the minimal MCP endpoints below. This example exposes a few generic endpoints a client might call: `/mcp/connect`, `/mcp/context`, and `/mcp/notify`. Adapt payload shapes to match your MCP consumer.

```javascript
// server.js - Minimal MCP server example
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');
const fs = require('fs');
const path = require('path');

const app = express();
const port = process.env.PORT || 3000;

app.use(cors());
app.use(bodyParser.json({ limit: '2mb' }));

// Simple health check
app.get('/', (req, res) => {
  res.send({ status: 'ok', name: 'Minimal MCP server' });
});

// Client connects and provides metadata (e.g., clientId, workspace root)
app.post('/mcp/connect', (req, res) => {
  const info = req.body || {};
  console.log('MCP connect:', info);
  // Respond with server capabilities and session id
  res.json({ sessionId: Date.now().toString(), capabilities: { context: true, notifications: true } });
});

// Provide context for a file or workspace. Payload depends on client expectations.
// Example request payload: { path: "c:/path/to/file.ts" }
app.post('/mcp/context', (req, res) => {
  const { path: filePath } = req.body || {};
  if (!filePath) return res.status(400).json({ error: 'missing path' });

  try {
    const content = fs.readFileSync(filePath, 'utf8');
    // A real MCP server would analyze and return richer context (AST, symbols, diagnostics)
    res.json({ file: filePath, content, timestamp: Date.now() });
  } catch (err) {
    res.status(404).json({ error: 'file-not-found', details: err.message });
  }
});

// Notifications from client (edits, saves, events)
app.post('/mcp/notify', (req, res) => {
  const evt = req.body || {};
  console.log('MCP notify:', JSON.stringify(evt).slice(0, 1000));
  res.json({ ok: true });
});

app.listen(port, () => {
  console.log(`Minimal MCP server listening at http://localhost:${port}`);
});
```

Save `server.js`, then run the server:

```powershell
node server.js
```

You should see:

```
Minimal MCP server listening at http://localhost:3000
```

---

## 2) Example client interaction (curl / PowerShell)

After the server is running, test the endpoints from PowerShell.

```powershell
# Connect
$connect = @{ clientId = 'vscode-mcp-test'; root = 'C:/path/to/workspace' } | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri http://localhost:3000/mcp/connect -Body $connect -ContentType 'application/json'

# Request file context (adjust path to a real file in your workspace)
$req = @{ path = 'C:/path/to/workspace/src/app/app.module.ts' } | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri http://localhost:3000/mcp/context -Body $req -ContentType 'application/json'

# Send a notification
$evt = @{ event = 'fileSaved'; path = 'C:/path/to/workspace/src/app/app.component.ts' } | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri http://localhost:3000/mcp/notify -Body $evt -ContentType 'application/json'
```

If the server returns JSON responses, your MCP client (or VS Code extension) should be able to do the same.

---

## 3) Configure VS Code to use the MCP server

Most MCP-capable clients expose a setting to point to an MCP server URL. The setting's exact name depends on the extension; below is a generic example you can add to your workspace `.vscode/settings.json` or your user settings in VS Code.

```json
{
  "mcp.serverUrl": "http://localhost:3000",
  "mcp.connectOnOpen": true,
  "mcp.requestTimeoutMs": 20000
}
```

If your extension uses a different key (e.g., `mcpClient.server`, `modelContext.serverUrl`), update the appropriate setting name. Consult the extension's documentation for the exact configuration key.

If the client supports authentication (tokens), add the token header configuration in the extension settings and update the server to validate the header.

---

## 4) Adapting the server for Angular

A real Angular-aware MCP server should:
- Resolve the workspace root and read `angular.json` and `tsconfig.json`.
- Use TypeScript compiler APIs (`typescript` package) to parse files and provide AST, symbol tables, and diagnostics.
- Provide component-level metadata (decorator info, templates, template references) to the client.

Helpful Node packages:
- `typescript` — for parsing and program analysis
- `@angular/compiler` — to parse templates and analyze Angular metadata
- `rxjs` — for event/stream handling

High-level steps to add Angular context:
1. Read `angular.json` to find project roots.
2. Load `tsconfig.json` and create a `ts.Program` (TypeScript compiler API).
3. When `/mcp/context` is called for a `.ts` or `.html` file, return parsed AST or summarized symbols (exports, classes, decorators, inputs/outputs).
4. Provide diagnostics by running TypeScript's `getSemanticDiagnostics()` and `getSyntacticDiagnostics()`.

This is a non-trivial task; start by returning file contents (the minimal example) and iterate by adding TypeScript analysis when needed.

---

## 5) Security and deployment notes

- Run MCP servers behind TLS (HTTPS) in production and require authentication.
- Limit allowed client origins (CORS) or require API keys to avoid unauthorized access to workspace files.
- Do not expose workspace root paths or sensitive files over MCP without proper access controls.
- If installing on a remote host, consider SSH tunnels or VPN to protect traffic between VS Code and the MCP server.

---

## 6) Debugging tips

- Confirm the server is reachable: `Invoke-RestMethod http://localhost:3000/` or open the address in a browser.
- Check logs printed by the server for incoming requests and errors.
- If VS Code refuses to connect, ensure the extension config is pointing to the exact URL (including port) and there are no firewall rules blocking the port.
- Use `curl` or PowerShell `Invoke-RestMethod` to reproduce client requests and inspect raw responses.

---

## 7) Next steps & extension ideas

- Implement a simple TypeScript analysis route that returns exported symbols for a file.
- Add template parsing using `@angular/compiler` to return component template metadata.
- Add an event stream endpoint (Server-Sent Events or WebSocket) for push notifications (file changes, diagnostics updates) instead of polling.
- Build adapter code if your MCP consumer expects a different JSON schema.

---

## References & further reading
- TypeScript compiler API: https://github.com/microsoft/TypeScript/wiki/Using-the-Compiler-API
- Angular compiler (template) API: https://angular.io/guide/aot-compiler
- Express: https://expressjs.com/

---

If you'd like, I can:
- Scaffold the full example `server.js` and `package.json` in the repo (I can create files now).  
- Add a small TypeScript-based MCP server example that returns TypeScript symbol summaries for requested files.

Tell me which next step you'd prefer and I'll implement it.
