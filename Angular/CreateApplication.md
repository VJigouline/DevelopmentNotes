# Creating an Angular Application — Notes & Quick Start

This document explains how to create a new Angular application from scratch, covering prerequisites, the Angular CLI, options when creating a project, common development commands, testing, building for production, and deployment tips. Copy-ready PowerShell commands are included.

## Prerequisites
- Node.js LTS and `npm` installed. Verify:

```powershell
node --version
npm --version
```

- Angular CLI (install globally or use `npx`):

```powershell
npm install -g @angular/cli
# or use npx: npx -p @angular/cli ng new my-app
```

- Recommended editor: Visual Studio Code with the **Angular Language Service** extension.

## Create a new app using Angular CLI

The most common way to create a new Angular app is with `ng new`.

```powershell
ng new my-angular-app
cd my-angular-app
```

By default the CLI prompts for:
- Add Angular routing? (Yes/No)
- Stylesheet format (CSS, SCSS, SASS, Less, Stylus)

Use flags to skip prompts and set options up-front:

```powershell
ng new my-angular-app --routing --style=scss --skip-git
```

Key flags:
- `--routing` — scaffold Router setup and a module for routes
- `--style` — choose stylesheet format
- `--strict` — enable strict mode (strongly recommended)
- `--skip-install` — create project without running `npm install`
- `--directory` — specify directory name

## Workspace vs Single App
- Newer CLI versions create a workspace that may contain multiple projects. A simple `ng new` creates one app with workspace scaffolding supporting libraries.
- Use `ng generate application` for additional apps inside the same workspace.

## Project structure (overview)
```
my-angular-app/
├── src/
│   ├── app/
│   │   ├── app.module.ts
│   │   ├── app.component.ts
│   │   └── ...
│   ├── assets/
│   └── index.html
├── angular.json
├── package.json
├── tsconfig.json
└── README.md
```

## Development server
Run the dev server with live reload:

```powershell
ng serve
# or open browser automatically:
ng serve --open
# use a specific port:
ng serve --port 4300 --open
```

The default URL is `http://localhost:4200`.

## Generate code (schematics)
Use the CLI to scaffold components, services, modules, pipes, directives, etc:

```powershell
ng generate component components/my-component
ng generate service services/my-service
ng generate module modules/my-module
# short forms:
ng g c components/my-component
ng g s services/my-service
ng g m modules/my-module
```

Use `--flat`, `--module`, `--export` and other flags to control placement and module linkage.

## Build for production

```powershell
ng build
# production build with optimization
ng build --configuration production
# or short:
ng build --prod
```

Output is placed in `dist/<project-name>/`.

## Serve production build locally (simple)

```powershell
npm install -g http-server
http-server ./dist/my-angular-app -p 8080
# or use a small node static server
npx serve ./dist/my-angular-app
```

## Testing

- Unit tests (Karma + Jasmine by default):

```powershell
ng test
```

- End-to-end tests (Protractor for older CLI; newer projects may use Cypress or WebDriver):

```powershell
ng e2e
```

## Linting

Angular projects often include ESLint (older projects used TSLint). Run linting via:

```powershell
ng lint
# or npm script
npm run lint
```

## Style, assets, and environments
- `src/styles.*` — global styles; component styles are scoped per component.
- `assets/` — static assets copied to the `dist` folder.
- `src/environments/` — environment-specific settings (e.g., `environment.ts`, `environment.prod.ts`).

## Internationalization (i18n)
Angular has built-in i18n tooling. Use the CLI and `@angular/localize` for translations.

## Deployment
- Build with `ng build --configuration production` and upload the contents of `dist/` to your static host (Netlify, Vercel, S3 + CloudFront, Azure Static Web Apps, Firebase Hosting).
- For SPA routing to work on static hosts, configure fallback to `index.html` for unknown routes (e.g., rewrite rules).

## Continuous Integration
- Add CI steps to install dependencies, run `ng build --configuration production`, and run tests.
- Example quick CI commands:

```powershell
npm ci
npm run build --if-present
npm test -- --watch=false
```

## Using Angular with Docker (optional)

Create a simple Dockerfile:

```dockerfile
FROM node:18-alpine as builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build -- --configuration production

FROM nginx:stable-alpine
COPY --from=builder /app/dist/my-angular-app /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## VS Code tips
- Install **Angular Language Service**, **ESLint**, and optionally **Prettier**.
- Use workspace `tsconfig.json` for better IntelliSense.
- Add helpful `launch.json` and `tasks.json` for debugging and common tasks.

Example `.vscode/launch.json` for debugging Angular in Chrome:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Launch Chrome against localhost",
      "type": "pwa-chrome",
      "request": "launch",
      "url": "http://localhost:4200",
      "webRoot": "${workspaceFolder}/src"
    }
  ]
}
```

## Common troubleshooting
- **ng command not found**: ensure `@angular/cli` is installed globally or use `npx ng`.
- **Port already in use**: use `--port` flag or kill the process using the port.
- **Slow initial build**: first build can be slow, enable `--watch` and use incremental builds for dev.
- **Module not found**: run `npm install` and verify `tsconfig.json` paths.

## Quick copy-ready checklist (PowerShell)

```powershell
# install CLI (if needed)
npm install -g @angular/cli

# create project
ng new my-angular-app --routing --style=scss --strict
cd my-angular-app

# serve in dev
ng serve --open

# generate component
ng g c components/example

# build production
ng build --configuration production
```

## Next steps & resources
- Angular documentation: https://angular.io/docs
- Angular CLI: https://angular.io/cli
- Angular tutorial: https://angular.io/tutorial

---

If you want, I can scaffold a minimal example app in this repo (`package.json`, `src/` with `app` components) so you can run `ng serve` immediately — should I create that? 
