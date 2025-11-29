# Installing NVM (Node Version Manager)

This page explains how to install and use Node Version Manager (NVM) on Windows, macOS, and Linux. There are two popular projects:

- `nvm-sh` (https://github.com/nvm-sh/nvm) — the standard NVM for macOS and Linux (shell script).
- `nvm-windows` (https://github.com/coreybutler/nvm-windows) — a native Windows port for PowerShell/Command Prompt.

Choose the one that matches your OS. Do not install both on the same machine unless you understand how they interact with PATH.

---

## Quick checks (before installing)

- If you already have Node.js installed via the system installer, consider uninstalling it first to avoid PATH conflicts.
- Decide whether you want a per-user install (recommended) or system-wide (Windows/more advanced).

---

## 1) Install on macOS / Linux (nvm-sh)

nvm-sh installs as a shell script that modifies your shell startup files. The recommended installation method is the official install script.

1. Install using the official install script (curl):

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

Or (wget):

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

2. After install, either close/reopen your terminal or source your shell profile:

```bash
# Bash
source ~/.bashrc
# or (macOS with Homebrew zsh default)
source ~/.zshrc

# Verify nvm is available
nvm --version
```

3. Basic usage examples:

```bash
# List available remote Node versions
nvm ls-remote

# Install latest LTS version
nvm install --lts

# Install a specific version
nvm install 18.17.1

# List installed versions
nvm ls

# Use a version for the current shell session
nvm use 18.17.1

# Set a default (alias) version
nvm alias default 18.17.1

# Per-project: create a .nvmrc with the version string (e.g. "18" or "18.17.1")
echo "18" > .nvmrc
nvm use # reads .nvmrc
```

Notes:
- `nvm use` affects the current shell session. Add `nvm alias default <version>` to make a default for new shells.
- Use `.nvmrc` to pin a project version and run `nvm use` when entering the project directory (or add `nvm use` to your shell-based project automation).

---

## 2) Install on Windows (nvm-windows)

nvm-windows is a different implementation designed for Windows. It manages Node versions and updates your PATH for Command Prompt / PowerShell.

Options:

A) Installer (recommended)

1. Download the latest installer from the `nvm-windows` releases page:
	https://github.com/coreybutler/nvm-windows/releases

2. Run the installer (`nvm-setup.exe`) and follow prompts. Choose a directory for `nvm` (default is fine) and for your Node installations.

3. After install, open a new PowerShell or Command Prompt and verify:

```powershell
nvm --version
```

B) Chocolatey (if you use Chocolatey)

```powershell
# Requires elevated PowerShell
choco install nvm -y
# Restart terminal
nvm --version
```

Basic usage (nvm-windows uses similar commands):

```powershell
# List available versions
nvm list available

# Install a version
nvm install 18.17.1

# List installed versions
nvm list

# Use a version (sets system-wide/current shell)
nvm use 18.17.1

# Uninstall a version
nvm uninstall 18.17.1
```

Notes:
- `nvm-windows` stores Node versions in the install directory you selected during setup. It replaces the PATH entries for `node` and `npm` when switching.
- If you previously had Node installed from the official Windows installer, uninstall it first to avoid conflicts.

---

## 3) Verifying installation

After installing, check:

```bash
# Verify node and npm are the expected versions
node --version
npm --version

# Verify nvm is working
nvm --version
nvm ls
```

On Windows use PowerShell commands above (replace `bash` with `powershell` where appropriate).

---

## 4) Using `.nvmrc` for per-project versions

Add a `.nvmrc` file at the root of your project with the desired version (example: `14`, `18`, `lts/*`, or `18.17.1`).

Example:

```bash
# Write desired version
echo "18" > .nvmrc

# Switch to it
nvm use
```

Tip: Add a small script or shell hook to automatically call `nvm use` when entering project directories (many developers use direnv or shell-specific hooks).

---

## 5) Troubleshooting

- `nvm: command not found` after install (macOS/Linux): ensure your shell profile sources the `nvm` initialization lines the install script added, e.g.:

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # This loads nvm bash_completion
```

- PATH issues on Windows: restart terminal after installing `nvm-windows`. Make sure old Node installer entries were removed from `PATH`.

- `permission denied` while installing global packages: prefer installing global developer tools with `npm install -g` when using nvm (because each node install is in your user scope). Avoid `sudo` with nvm-managed Node.

- CI environments: use `nvm` or the platform-provided Node actions (e.g., `actions/setup-node@v4`) for reproducible installs. Avoid writing `http.*.extraheader` entries to git configs — that's unrelated but sometimes seen in CI credential helpers.

---

## 6) Uninstalling nvm

- macOS/Linux (`nvm-sh`): remove `~/.nvm` directory and the lines the installer added to your shell profile files (e.g., `~/.bashrc`, `~/.zshrc`).

```bash
rm -rf "$HOME/.nvm"
# remove lines that source nvm from your shell startup files
```

- Windows (`nvm-windows`): use the Control Panel or Settings to uninstall the `nvm` program, and remove the Node versions folder if needed.

---

## 7) References

- nvm (nvm-sh) repository: https://github.com/nvm-sh/nvm
- nvm-windows repository: https://github.com/coreybutler/nvm-windows
- nvm-sh install docs: https://github.com/nvm-sh/nvm#install--update-script
- nvm-windows releases: https://github.com/coreybutler/nvm-windows/releases

---

If you want, I can also:

- Add a short `getting-started` script that installs the latest LTS and creates a `.nvmrc` for a project.
- Add CI examples (GitHub Actions) that use `actions/setup-node` or `nvm` directly.

---


