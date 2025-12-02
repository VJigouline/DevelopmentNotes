# Angular ng CLI Commands Reference

Complete reference for Angular's command-line interface (CLI). These commands simplify Angular development from project creation to deployment.

---

## Prerequisites

- Node.js and npm installed (see `InstallNVM.md` for setup)
- Angular CLI installed globally: `npm install -g @angular/cli`
- Verify installation: `ng --version`

---

## 1) Project scaffolding

### ng new — create a new Angular application

```bash
# Basic: create new app with default settings
ng new my-app

# Create app with strict mode, routing, and SCSS
ng new my-app --strict --routing --style=scss

# Create without installing npm packages (install later)
ng new my-app --skip-install

# Create monorepo workspace (Nx-like structure)
ng new my-workspace --create-application=false
```

**Options:**
- `--routing` — adds Angular Router module and routing setup
- `--style=css|scss|sass|less|styl` — default stylesheet format
- `--skip-install` — skip npm install
- `--strict` — enable strict TypeScript and Angular checks
- `--package-manager=npm|yarn|pnpm` — specify package manager
- `--create-application=false` — create workspace without initial app (for monorepo)

---

### ng generate (ng g) — create components, services, modules, and more

```bash
# Generate a component
ng g component components/my-component

# Generate a service
ng g service services/my-service

# Generate a module with routing
ng g module modules/my-module --routing

# Generate a guard (auth, canActivate, etc.)
ng g guard guards/auth-guard

# Generate a directive
ng g directive directives/my-directive

# Generate a pipe
ng g pipe pipes/my-pipe

# Generate a class
ng g class models/my-model

# Generate an interface
ng g interface models/my-interface

# Generate an enum
ng g enum models/my-enum

# Generate a resolver (for route data preloading)
ng g resolver resolvers/my-resolver

# Generate in a specific folder without creating subfolder
ng g component my-component --skip-tests

# Generate with all test files
ng g component my-component --spec
```

**Common options:**
- `--module=path/to.module` — specify module to import into
- `--skip-tests` — don't generate .spec.ts files
- `--skip-import` — don't add to module imports
- `--prefix` — custom component selector prefix
- `--style=scss` — override default stylesheet format
- `--standalone` — create as standalone component (Angular 14+)

**Schematics list:**
```bash
ng g --help  # shows all available schematics
```

---

## 2) Development server

### ng serve — run development server with live reload

```bash
# Start dev server (default localhost:4200)
ng serve

# Serve on different port
ng serve --port 3000

# Open in browser automatically
ng serve --open

# Serve with source maps (easier debugging)
ng serve --source-map

# Serve with source maps disabled (faster)
ng serve --source-map=false

# Disable file watching
ng serve --watch=false

# Poll for file changes (useful in Docker/VMs)
ng serve --poll=2000

# Serve with custom host
ng serve --host 0.0.0.0 --port 4200

# Enable SSL (HTTPS)
ng serve --ssl
```

**Options:**
- `--port=port` — port number (default 4200)
- `--open` — open browser automatically
- `--watch=true|false` — enable/disable file watching
- `--source-map=true|false` — generate source maps for debugging
- `--poll=ms` — file change poll interval in milliseconds
- `--host=host` — listening hostname (default localhost)
- `--ssl` — enable HTTPS with self-signed certificate
- `--live-reload=true|false` — live reload on changes

---

## 3) Building for production

### ng build — compile application for deployment

```bash
# Build for production (optimized, minified)
ng build

# Build with configuration (default is 'development')
ng build --configuration production

# Output to custom directory
ng build --output-path=dist/my-app

# Set base href for hosting under subpath
ng build --base-href=/my-app/

# Named configurations
ng build --configuration staging

# Build with stats file (analyze bundle size)
ng build --stats-json

# Build with source maps
ng build --source-map

# Build with source maps disabled (faster, smaller)
ng build --source-map=false

# Preload critical modules
ng build --preload-chunks

# Delete output directory before build
ng build --delete-output-path
```

**Options:**
- `--configuration=dev|prod` — build configuration (see `angular.json`)
- `--output-path=path` — output directory
- `--base-href=/path/` — base URL for relative URLs
- `--source-map=true|false` — generate source maps
- `--stats-json` — generate build stats (for analysis)
- `--delete-output-path=true|false` — clear dist before build
- `--aot=true|false` — ahead-of-time compilation (default true in prod)
- `--optimization=true|false` — enable optimization

---

## 4) Testing

### ng test — run unit tests (Karma + Jasmine)

```bash
# Run tests once
ng test --watch=false

# Run tests with coverage
ng test --code-coverage

# Run specific test file
ng test --include='**/my-component.spec.ts'

# Run tests in headless Chrome
ng test --watch=false --browsers=ChromeHeadless

# Run tests with browsers specified
ng test --browsers=Chrome,Firefox

# Run with debugging (opens inspector)
ng test --browsers=Chrome

# Generate coverage report
ng test --code-coverage --watch=false

# Watch for changes and re-run
ng test --watch=true
```

**Options:**
- `--watch=true|false` — watch mode (default true)
- `--code-coverage` — generate coverage report in `coverage/` directory
- `--browsers=browser` — Chrome, ChromeHeadless, Firefox, etc.
- `--include` — pattern for files to include
- `--exclude` — pattern for files to exclude
- `--source-map=true|false` — generate source maps for debugging

Coverage report location: `coverage/<project-name>/index.html`

---

### ng e2e — run end-to-end tests (Protractor or Cypress)

```bash
# Run e2e tests
ng e2e

# Run specific test suite
ng e2e --specs='src/e2e/my-test.e2e-spec.ts'

# Run without watch
ng e2e --watch=false

# Headless mode
ng e2e --headless
```

---

## 5) Linting

### ng lint — check code quality and style

```bash
# Run linter (ESLint or TSLint depending on config)
ng lint

# Fix issues automatically
ng lint --fix

# Show warnings
ng lint --force

# Specific file
ng lint my-component.ts
```

**Options:**
- `--fix` — automatically fix fixable issues
- `--force` — show warnings as well as errors

---

## 6) Updating Angular

### ng update — safely update Angular and dependencies

```bash
# Check which packages can be updated
ng update

# Update Angular CLI and core
ng update @angular/cli @angular/core

# Update to next major version (with migrations)
ng update @angular/cli --allow-dirty

# Update all packages
ng update --all

# Run specific schematic migration
ng update @angular/core --from=14.0.0 --to=15.0.0
```

**Options:**
- `--allow-dirty` — allow uncommitted changes
- `--force` — force update even if dependencies conflict
- `--next` — include pre-release versions

---

## 7) Configuration and analysis

### ng config — view/set Angular configuration

```bash
# Get current configuration value
ng config projectType

# Set configuration value
ng config schematics.@schematics/angular:component.style "scss"

# View entire angular.json
ng config --global

# List all config options
ng config --list
```

---

### ng doc — open Angular documentation in browser

```bash
# Open main Angular docs
ng doc

# Search for specific topic
ng doc forms
ng doc routing
ng doc http-client
```

---

## 8) Standalone mode (Angular 14+)

### Create standalone components and applications

```bash
# Generate standalone component
ng g component my-component --standalone

# Generate standalone app (new in Angular 17+)
ng new my-app --standalone

# Create standalone service
ng g service my-service --standalone
```

Standalone components don't require NgModule; use in bootstrapApplication:

```ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent);
```

---

## 9) Library generation (schematics)

### ng generate library — create reusable library

```bash
# Create library in workspace
ng generate library my-lib

# Create library with routing
ng generate library my-lib --routing

# Create library in specific path
ng generate library my-lib --directory=libs

# Generate library with specified prefix
ng generate library my-lib --prefix=lib
```

Build and publish:

```bash
# Build library
ng build my-lib

# Publish to npm
npm publish dist/my-lib

# Use in another app
npm install my-lib
```

---

## 10) Deployment utilities

### ng deploy — deploy to hosting platform

```bash
# Deploy to default platform (set in angular.json)
ng deploy

# Deploy with specific configuration
ng deploy --configuration production

# Deploy to Firebase
ng add @angular/fire
ng deploy
```

Common deployment targets:
- Firebase Hosting
- Azure Static Web Apps
- GitHub Pages
- Netlify
- Vercel
- AWS S3

---

## 11) Version and diagnostics

### ng version — show Angular CLI and package versions

```bash
ng --version
ng -v
```

Outputs Angular, CLI, Node, npm versions, and all installed packages.

---

### ng help — show CLI help

```bash
# General help
ng help

# Command-specific help
ng help build
ng help serve
ng help generate
```

---

## 12) Less common / advanced commands

### ng extract-i18n — extract translatable strings

```bash
# Extract strings to XLIFF format
ng extract-i18n

# Custom output format
ng extract-i18n --output-path=src/locale
```

---

### ng run — execute custom Angular CLI builders

```bash
# Run custom builder defined in angular.json
ng run my-app:build

# Run with options
ng run my-app:serve --port=3000
```

---

## Common workflow commands (copy/paste)

**Quick start:**
```bash
ng new my-app --routing --style=scss
cd my-app
ng serve --open
```

**Add a feature:**
```bash
ng g module features/my-feature --routing
ng g component features/my-feature/pages/my-feature
ng g service features/my-feature/services/my-feature
ng g guard features/my-feature/guards/my-feature-guard
```

**Build for production:**
```bash
ng build --configuration production --base-href=/
# Or:
ng build --prod --base-href=/
```

**Run tests:**
```bash
ng test --watch=false --code-coverage
```

**Update Angular:**
```bash
ng update @angular/cli @angular/core
npm install
ng serve
```

**Deploy to Azure Static Web Apps:**
```bash
ng build --configuration production --output-path=dist/my-app --base-href=/
# Then push to repo to trigger GitHub Action
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `ng: command not found` | Install globally: `npm install -g @angular/cli` |
| `Port 4200 already in use` | Use different port: `ng serve --port 3000` |
| `Module not found` error | Run `npm install` or check import paths |
| Slow rebuild | Disable source maps: `ng serve --source-map=false` |
| Build fails after upgrade | Run `ng update @angular/cli @angular/core` and follow migrations |
| Old files cached in browser | Hard refresh (Ctrl+Shift+R) or clear cache, disable service worker |
| Component not generating | Check module path: `ng g component --module=path/to/module` |

---

## Performance tips

- Use `--source-map=false` in `ng serve` for faster development builds
- Use `--optimization` with `ng build` for smaller production bundles
- Lazy load feature modules with routing
- Use `OnPush` change detection strategy
- Analyze bundle size: `ng build --stats-json` then use webpack-bundle-analyzer
- Enable budgets in `angular.json` to track bundle growth

---

## Resources

- Official ng docs: https://angular.io/cli
- Angular API reference: https://angular.io/api
- Angular style guide: https://angular.io/guide/styleguide
- Schematics documentation: https://angular.io/guide/schematics
- Angular update guide: https://update.angular.io

---

**Pro tip**: Use `ng help <command>` to see all options for any command, or `ng <command> --help` for detailed information.