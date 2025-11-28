
# Updating Dependencies in an Angular Application — Safe Steps & Commands

This note shows a safe, repeatable workflow for updating Angular itself and other project dependencies. It covers `ng update` (recommended for Angular core + CLI), `npm`/`pnpm`/`yarn` approaches, `npm-check-updates` for broader upgrades, and common troubleshooting steps.

Always work on a clean working tree and create a branch before making updates.

## High-level process

1. Create a branch and commit current state.
2. Update Angular CLI and Angular core using `ng update` (this runs migrations).
3. Update other Angular libraries (Material, CDK) with `ng update`.
4. Update non-Angular dependencies (`npm update`, `npx npm-check-updates`, or package manager-specific commands).
5. Run tests, lint, type-check and a production build.
6. Fix breaking changes and run migrations provided by libraries.
7. Commit and open a PR; run CI.

## 0) Prepare: commit & branch

```powershell
# Make sure working tree is clean
git status

# Create a branch for the update work
git checkout -b chore/update-deps

# Commit any local changes first
git add -A
git commit -m "WIP: save work before updating deps" || echo "no changes to commit"
```

## 1) Update Angular CLI globally (optional) and locally

Update the global CLI if you use it interactively (optional):

```powershell
npm install -g @angular/cli
ng version
```

Update the workspace CLI and core packages using Angular's recommended tool — `ng update`. This runs code migrations when available.

```powershell
# Show recommended updates
ng update

# Update Angular core and CLI (major upgrade will be prompted)
ng update @angular/cli @angular/core
```

If you're targeting a specific major (for example v15→v16), `ng update` will show the required command and run migrations.

## 2) Update Angular libraries (Material, CDK, etc.)

```powershell
# Example: Angular Material
ng update @angular/material

# Update other official packages as suggested by ng update
ng update @angular/router @angular/forms
```

Note: `ng update` will apply migrations (tsconfig changes, RxJS/TypeScript compatibility, etc.). Review the migration output and test thoroughly.

## 3) Check outdated packages

Quick status with npm:

```powershell
npm outdated
```

This shows current, wanted, and latest versions for your dependencies.

## 4) Update non-Angular packages

Option A — safe, non-major updates (uses `npm`):

```powershell
# Update packages to the latest minor/patch versions according to semver
npm update
# Or if using pnpm
pnpm update
# Or yarn
yarn upgrade
```

Option B — upgrade all packages (including majors) with `npm-check-updates` (ncu).

Install `npm-check-updates` and bump package.json versions (PowerShell):

```powershell
npx npm-check-updates -u
# Inspect package.json changes, then install
npm install
```

For `pnpm`:

```powershell
npx npm-check-updates -u
pnpm install
```

For `yarn` (v1):

```powershell
npx npm-check-updates -u
yarn install
```

Important: `npx npm-check-updates -u` will update package.json to latest versions (including breaking majors). Use with caution — prefer to update one major at a time and run tests.

## 5) Update transitive dependencies (lockfile)

To refresh lockfile and get reproducible install:

```powershell
# npm
rm -r node_modules package-lock.json; npm install

# pnpm
pnpm install --force

# yarn (classic)
rm -r node_modules yarn.lock; yarn install
```

Or with npm: `npm ci` for clean installs (uses lockfile).

## 6) Run checks: tests, lint, build

Run the standard verification steps immediately after updates:

```powershell
npm run lint
npm test -- --watch=false
npm run build --if-present
# For Angular specifically
ng build --configuration production
ng test --watch=false
```

Fix TypeScript errors, runtime failures, and lint warnings as they appear.

## 7) Handle peer dependency and breaking-change errors

- If you get peer dependency errors, follow the package's recommended versions. Sometimes you need to update related packages together (e.g., RxJS for certain Angular versions).
- Read `ng update` output carefully — it often lists migrations and manual steps.
- Check the library changelog for breaking changes.

## 8) Use the Angular Update Guide

For major Angular upgrades (e.g., v14 → v15 → v16), consult the official interactive guide: https://update.angular.io/ — it provides step-by-step instructions and compatibility notes.

## 9) Migrating RxJS / TypeScript

- `ng update` may update TypeScript and RxJS versions. Ensure your `tsconfig.json` and code are compatible with the new TypeScript target.
- Run `ng lint` and `ng test` to catch subtle issues.

## 10) Commit changes and open a PR

```powershell
git add package.json package-lock.json yarn.lock pnpm-lock.yaml
git commit -m "chore(deps): update dependencies"
git push origin HEAD
# Create a PR from your branch
```

## 11) Testing in CI and staging

- Ensure CI runs `npm ci` / `pnpm install` and runs tests/build. Fix CI failures before merging.
- Deploy to staging and smoke test the app.

## 12) Rollback plan

- If the update causes blocking regressions, revert the branch or use the previous commit to restore the lockfile:

```powershell
git revert <commit-hash>  # or reset to the previous commit
```

## Troubleshooting tips

- "Module not found" after update: delete `node_modules` and lockfile, then reinstall.
- Type errors after updating TypeScript: check `tsconfig` `target`/`lib` and update code for stricter checks.
- Unmet peer dependency warnings: align versions across dependent packages or install the required peer.
- Runtime errors in browser: check polyfills and browser targets (targets in `tsconfig`/browserslist).
- If `ng update` stalls or fails: run with `--verbose` and inspect logs.

## Quick PowerShell cheat-sheet

```powershell
# 1. Branch and backup
git checkout -b chore/update-deps
git add -A; git commit -m "WIP: pre-update" || echo "no changes"

# 2. Angular core update
ng update @angular/cli @angular/core

# 3. Official Angular packages
ng update @angular/material

# 4. Review outdated
npm outdated

# 5. Safe minor/patch updates
npm update

# 6. Bump all (majors) with caution
npx npm-check-updates -u
npm install

# 7. Clean install and build checks
rm -r node_modules package-lock.json
npm install
npm run lint
npm test -- --watch=false
ng build --configuration production

# 8. Commit
git add package.json package-lock.json
git commit -m "chore(deps): update dependencies"
```

## Extra: Automating dependency updates

- Use Dependabot (GitHub) or Renovate to automatically open PRs for dependency updates (configurable for minor/major policies).
- These PRs run CI and make updating safer.

## References

- Angular Update Guide: https://update.angular.io/
- ng update docs: https://angular.io/cli/update
- npm-check-updates: https://www.npmjs.com/package/npm-check-updates
- Renovate: https://www.whitesourcesoftware.com/free-developer-tools/renovate/

---
If you'd like, I can:
- Scaffold a `dependabot.yml` or Renovate config for this repo, or
- Run a dry update locally and create a branch with the updated `package.json` and lockfile for you to review.
Which would you prefer?

