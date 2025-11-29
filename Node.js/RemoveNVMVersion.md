# Removing NVM Versions

This guide explains how to uninstall specific Node versions managed by NVM on macOS, Linux, and Windows. Both `nvm-sh` and `nvm-windows` make it easy to remove unused versions to free up disk space.

---

## Prerequisites

- NVM installed (see `InstallNVM.md` for installation steps)
- Verify you have the desired default version set before removing others
- Identify which Node version(s) you want to remove

---

## 1) Remove a version with nvm-sh (macOS / Linux)

The `nvm uninstall` command removes a specific Node version.

**Basic syntax:**

```bash
nvm uninstall <version>
```

**Examples:**

```bash
# Remove a specific version
nvm uninstall 14.21.0

# Remove by version number only
nvm uninstall 16

# Remove multiple versions (one command each)
nvm uninstall 14
nvm uninstall 15
nvm uninstall 17
```

**Verify it's removed:**

```bash
nvm ls
```

The removed version will no longer appear in the list.

**Important notes:**

- You **cannot** remove the currently active version. If you try, NVM will warn you:
  ```
  nvm: Cannot uninstall currently-active node version, v18.0.0 (inferred from .nvmrc).
  ```
  Switch to another version first:
  ```bash
  nvm use 18  # or another version
  nvm uninstall 16
  ```

- Removing the default alias requires switching the default first:
  ```bash
  nvm alias default 18  # set new default
  nvm uninstall 16      # then remove old one
  ```

---

## 2) Remove a version with nvm-windows (Windows)

nvm-windows uses the `nvm uninstall` command, which is similar to nvm-sh but with slightly different syntax and options.

**Basic syntax:**

```powershell
nvm uninstall <version>
```

**Examples:**

```powershell
# Remove a specific version
nvm uninstall 14.21.0

# Remove by version number
nvm uninstall 16

# Remove multiple versions
nvm uninstall 14
nvm uninstall 15
nvm uninstall 17
```

**Verify it's removed:**

```powershell
nvm list
```

**Important notes:**

- Like nvm-sh, you cannot uninstall the currently active version. Switch first:
  ```powershell
  nvm use 18
  nvm uninstall 16
  ```

- nvm-windows stores Node binaries in the directory you selected during nvm setup (typically `%APPDATA%\nvm`). Uninstalling removes those files.

---

## 3) View installed versions before removing

**On macOS/Linux (nvm-sh):**

```bash
nvm ls
```

Output example:
```
       v14.21.0
       v16.13.2
->     v18.17.1     (currently active)
       v20.0.0

default -> 18 (-> v18.17.1)
iojs -> N/A (default)
unstable -> N/A (default)
```

**On Windows (nvm-windows):**

```powershell
nvm list
```

Output example:
```
    18.17.1
  * 20.0.0 (currently active)
    14.21.0
```

---

## 4) Remove the currently active version

If you try to remove the active version, NVM will prevent it. Switch to a different version first:

**macOS/Linux:**

```bash
# See what's currently active
nvm current

# Switch to a different version
nvm use 18

# Now you can remove the previous version
nvm uninstall 16
```

**Windows:**

```powershell
# Check current version
node --version

# Use a different version
nvm use 18

# Remove the old one
nvm uninstall 16
```

---

## 5) Remove the default version

If you want to remove a version that's set as the default (alias), change the default first:

**macOS/Linux:**

```bash
# Check current default
nvm alias default

# Set a new default
nvm alias default 18

# Remove the old default
nvm uninstall 16
```

**Windows:**

nvm-windows does not support the `nvm alias` command. Instead, just switch to another version before uninstalling:

```powershell
nvm use 18
nvm uninstall 16
```

---

## 6) Bulk removal (remove all but one version)

If you want to keep only one version and remove all others:

**macOS/Linux:**

```bash
# Example: keep only v18.17.1, remove all others
nvm ls  # first, see all versions

# Remove each old version individually
nvm uninstall 14
nvm uninstall 16
nvm uninstall 20
```

Or create a simple script:

```bash
#!/bin/bash
# Keep only version passed as argument

KEEP_VERSION=$1

if [ -z "$KEEP_VERSION" ]; then
  echo "Usage: $0 <version-to-keep>"
  exit 1
fi

# Set as default first
nvm alias default "$KEEP_VERSION"
nvm use "$KEEP_VERSION"

# Remove all others
for version in $(nvm list | grep -v "current" | grep "v" | awk '{print $NF}'); do
  version_num="${version#v}"
  if [ "$version_num" != "$KEEP_VERSION" ]; then
    echo "Removing $version"
    nvm uninstall "$version_num"
  fi
done

echo "Kept only v$KEEP_VERSION"
```

Save as `keep-only.sh` and run:

```bash
chmod +x keep-only.sh
./keep-only.sh 18
```

**Windows:**

```powershell
# Manual: use the version to keep, then uninstall others one by one
nvm use 18
nvm uninstall 14
nvm uninstall 16
nvm uninstall 20
```

---

## 7) Check disk space before and after

**macOS/Linux:**

```bash
# Check space used by NVM versions
du -sh ~/.nvm/versions/node/

# Before removal
du -sh ~/.nvm/versions/node/v18.17.1

# After removal, check again
du -sh ~/.nvm/versions/node/
```

**Windows:**

```powershell
# Check NVM directory size (replace with your actual nvm path)
$nvmPath = "$env:APPDATA\nvm\nodejs"  # or wherever you installed

# Get total size
Get-ChildItem $nvmPath -Recurse | Measure-Object -Property Length -Sum
```

---

## 8) Troubleshooting

| Issue | Solution |
|-------|----------|
| `nvm: Cannot uninstall currently-active node version` | Switch to another version first with `nvm use <version>` |
| Version still appears after uninstall | Run `nvm cache clear` (macOS/Linux) or restart terminal (Windows) |
| Path issues after removal | Restart your terminal/PowerShell to refresh PATH |
| Disk space not freed (Windows) | Manually delete the version folder in `%APPDATA%\nvm\nodejs` |
| Multiple versions with same base number | Uninstall by full version: `nvm uninstall 18.0.0` not just `18` |

---

## 9) Manual removal (if NVM command fails)

**macOS/Linux:**

If `nvm uninstall` fails for some reason, you can manually remove the version:

```bash
# Find NVM directory
echo $NVM_DIR  # typically ~/.nvm

# Manually remove the version directory
rm -rf ~/.nvm/versions/node/v16.13.2
```

**Windows:**

If the NVM command doesn't work, manually navigate to and delete the version folder:

1. Open File Explorer
2. Navigate to `%APPDATA%\nvm\nodejs` (or your custom nvm install path)
3. Delete the folder for the version you want to remove (e.g., `v16.13.2`)
4. Restart PowerShell/Command Prompt

---

## 10) Verify removal and confirm functionality

After removing a version, verify:

```bash
# macOS/Linux
nvm ls

# Windows
nvm list

# Verify active version still works
node --version
npm --version
```

---

## 11) Remove NVM entirely (not just a version)

If you want to uninstall NVM and all Node versions:

**macOS/Linux:**

```bash
rm -rf ~/.nvm
```

Then remove the NVM initialization lines from your shell startup files (`~/.bashrc`, `~/.zshrc`, etc.).

**Windows:**

Use Control Panel → Uninstall Programs to uninstall NVM, or manually delete:

```powershell
Remove-Item -Recurse -Force $env:APPDATA\nvm
```

---

## 12) Best practices

1. **Keep LTS and current**: Maintain at least one LTS version and one current version.
2. **Test before removing**: Verify your projects work with the remaining versions.
3. **Document your choice**: If removing a version used by a project, update your `.nvmrc` and document the change.
4. **Free up space periodically**: Run `nvm ls` and remove versions older than 3-6 months if no projects use them.
5. **Use `.nvmrc`**: Always pin your project Node version to avoid accidentally using an old one.

---

## 13) Resources

- nvm-sh documentation: https://github.com/nvm-sh/nvm
- nvm-windows documentation: https://github.com/coreybutler/nvm-windows
- Node.js release schedule: https://nodejs.org/en/about/releases/

---

**Pro tip**: Before removing a version, search your projects for any `.nvmrc` files that reference it:

```bash
# macOS/Linux
grep -r "v16" ~/.nvmrc  # or search your project directories
find . -name ".nvmrc" -exec cat {} \;

# Windows PowerShell
Get-ChildItem -Path . -Name ".nvmrc" -Recurse | ForEach-Object { Write-Host "Found: $_" }
```