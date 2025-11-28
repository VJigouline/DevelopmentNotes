
# TypeScript — Installation & Quick Start

This document provides concise, copy-ready instructions to install and start using TypeScript on Windows (PowerShell). It covers Node.js, package managers (`npm`, `pnpm`, `yarn`), installing TypeScript, initializing a project, common commands, and troubleshooting tips.

## Prerequisites
- Windows with PowerShell (this guide uses Windows PowerShell syntax).
- Internet access to download Node.js and packages.

## 1) Install Node.js
Install Node.js (includes `npm`). Choose the LTS version from the official site:

- Download: https://nodejs.org/

After installing, verify versions in PowerShell:

```powershell
node --version
npm --version
```

If you prefer `nvm` for Windows, see: https://github.com/coreybutler/nvm-windows

## 2) Choose a package manager (optional)
You can use `npm`, `pnpm`, or `yarn`. `npm` is bundled with Node.js. `pnpm` and `yarn` are alternatives with different performance/lockfile semantics.

Install `pnpm` (optional):

```powershell
npm install -g pnpm
pnpm --version
```

Install `yarn` (optional):

```powershell
npm install -g yarn
yarn --version
```

Note: Global installs on Windows may require an elevated PowerShell if your global npm directory is under Program Files. If you prefer to avoid global installs, use `npx` / `pnpm dlx` / `yarn dlx`.

## 3) Create a new project
Create a folder and initialize a `package.json`:

```powershell
mkdir my-ts-project; cd my-ts-project
npm init -y
# or: pnpm init -y
# or: yarn init -y
```

## 4) Install TypeScript
Install TypeScript locally (recommended) so the project is reproducible.

```powershell
npm install --save-dev typescript
# or: pnpm add -D typescript
# or: yarn add -D typescript

# Check the local tsc via npx (uses project install):
npx tsc --version
```

Global install (not required):

```powershell
npm install -g typescript
tsc --version
```

## 5) Initialize `tsconfig.json`
Create a TypeScript configuration file with sensible defaults:

```powershell
npx tsc --init
```

Edit `tsconfig.json` as needed (rootDir/outDir, target, module, strict, etc.). Example minimal changes:

```json
{
	"compilerOptions": {
		"target": "ES2019",
		"module": "commonjs",
		"rootDir": "src",
		"outDir": "dist",
		"strict": true,
		"esModuleInterop": true
	}
}
```

## 6) Add basic scripts to `package.json`
Edit `package.json` to add build and watch scripts:

```json
"scripts": {
	"build": "tsc",
	"watch": "tsc -w",
	"start": "node ./dist/index.js"
}
```

Then run:

```powershell
npm run build
npm run watch   # for development
npm start       # after build
```

## 7) Helpful tools for development
- `ts-node` — run TypeScript directly (good for small scripts): `npm i -D ts-node`
- `@types/node` — Node.js types: `npm i -D @types/node`
- `eslint` + `@typescript-eslint/parser` — linting
- `esbuild` or `swc` — fast bundling/transpilation for production builds

Quick example with `ts-node`:

```powershell
npx ts-node src/index.ts
```

## 8) VS Code setup
- Install the official `TypeScript and JavaScript Language Features` (built-in) and optionally `ESLint`.
- Open the workspace folder and ensure `tsconfig.json` is at the project root so IntelliSense and the language server pick it up.

## 9) Common troubleshooting
- PATH / command not found: verify Node and npm are on PATH. Restart PowerShell after installing Node.
- Permission errors on global installs: run an elevated PowerShell or use a per-user global `npm` directory (or prefer local installs).
- Wrong `tsc` version: use `npx tsc` to ensure the project-local compiler runs.
- Source files not compiled: check `tsconfig.json` `include`/`exclude` and `rootDir` settings.
- VS Code not picking up types: ensure `typescript.tsdk` is not set to an incompatible global path unless intentional.

Commands to check versions and environment:

```powershell
node --version
npm --version
npx tsc --version
where.exe node
where.exe npm
```

## 10) Quick copy-ready checklist (PowerShell)

```powershell
# create project
mkdir my-ts-project; cd my-ts-project
npm init -y

# install typescript locally
npm install --save-dev typescript

# init tsconfig and build once
npx tsc --init
npx tsc

# run with ts-node (optional)
npm install --save-dev ts-node @types/node
npx ts-node src/index.ts
```

## References
- TypeScript: https://www.typescriptlang.org/
- Node.js: https://nodejs.org/
- `ts-node`: https://typestrong.org/ts-node/

---
If you'd like, I can also add a minimal example project (`src/index.ts`) and a `package.json` with scripts so this becomes runnable immediately — want me to scaffold that?

