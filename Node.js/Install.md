
# Node.js — Installation & Quick Start (Windows / PowerShell)

This page provides concise, copy-ready instructions to install Node.js on Windows (PowerShell), choose a package manager, verify your setup, and troubleshoot common issues.

## 1) Pick an installation method

- Official installer (recommended for most users):
	- Download the Windows installer (LTS recommended) from https://nodejs.org/
	- Run the MSI and follow the prompts (adds Node and `npm` to PATH).

- `nvm-windows` (recommended if you need multiple Node versions):
	- Project: https://github.com/coreybutler/nvm-windows
	- Use `nvm` to install and switch Node versions without touching global installs.

- Package managers / Chocolatey (automation):
	- Chocolatey: `choco install nodejs-lts` (requires Chocolatey)
	- Winget: `winget install OpenJS.NodeJS.LTS`

## 2) Verify the install
Open a new PowerShell window (restart after installing) and run:

```powershell
node --version
npm --version
```

If using `nvm-windows`:

```powershell
nvm list
nvm use 18.20.0
node --version
```

## 3) Package manager choices

- `npm` — bundled with Node, the default package manager. Works well for most projects.
- `pnpm` — fast, disk-efficient installation using a global content-addressable store. Install with `npm i -g pnpm`.
- `yarn` — alternative with a stable lockfile and workspaces support. Install with `npm i -g yarn`.

Tip: Prefer project-local installs (`devDependencies` / `dependencies`) and `npx`/`pnpm dlx`/`yarn dlx` for one-off tooling to avoid global permission issues.

## 4) Global install permission notes (Windows)

- Global `npm` installs may require elevation depending on your npm global directory. Options:
	- Run PowerShell as Administrator for the install (not ideal long-term).
	- Reconfigure npm to use a per-user global directory: https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally
	- Use local project installs where possible.

## 5) Quick project setup (PowerShell)

```powershell
# create a new folder and initialize
mkdir my-node-app; cd my-node-app
npm init -y

# install a dependency and a dev tool
npm install express
npm install --save-dev nodemon

# run scripts
npm run start   # if package.json has a start script
npx nodemon src/index.js  # run with nodemon without global install
```

## 6) Windows-specific checks and PATH

- If `node` or `npm` is not found after install, confirm they are on PATH and restart PowerShell.
- Use `where.exe node` and `where.exe npm` to find the executables:

```powershell
where.exe node
where.exe npm
```

If you installed Node via `nvm-windows`, `node` will be managed by `nvm` and the `where.exe` output may show the `nvm` shim.

## 7) Common troubleshooting

- 'command not found' or missing PATH: restart your shell, verify PATH includes the Node install folder.
- Permission / EACCES on global installs: prefer local installs, configure a per-user npm prefix, or use a version manager.
- TLS/certificate errors when installing packages: ensure your system clock is correct, your proxy/SSL interception isn't breaking certs, and that corporate proxies are configured (see `npm config set proxy` / `https-proxy`).
- Proxy or corporate firewall: configure `npm` with the proxy settings or use an internal registry mirror.
- Unexpected Node version: verify with `node --version` and use `nvm` to switch versions if needed.

## 8) Useful commands

```powershell
node --version
npm --version
npm config get prefix
where.exe node
where.exe npm
npx <tool>   # run a locally installed tool or download+run one-off
```

## 9) References

- Node.js downloads and docs: https://nodejs.org/
- nvm-windows: https://github.com/coreybutler/nvm-windows
- npm docs (configuration, permissions): https://docs.npmjs.com/

---
Would you like me to scaffold a minimal `my-node-app` example (`package.json`, `src/index.js`) in this repo so you can try the steps immediately? 

