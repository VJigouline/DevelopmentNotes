# Angular — Installation & Quick Start

This guide provides concise, copy-ready instructions to install Angular on Windows (PowerShell). It covers prerequisites, Angular CLI setup, creating a new project, running the dev server, and common commands.

## Prerequisites

- **Node.js & npm**: Angular requires Node.js (LTS recommended) and npm.
  - Download from https://nodejs.org/
  - Verify with: `node --version` and `npm --version` in PowerShell

- **Code editor**: VS Code recommended (https://code.visualstudio.com/)
  - Install the **Angular Language Service** extension for better IntelliSense.

- **PowerShell execution policy**: If you get `npm.ps1 cannot be loaded` error, resolve it first (see [PowerShell npm.ps1 fix](../PowerShell/NpmCannotBeLoaded.md)).

## 1) Install Angular CLI globally

```powershell
npm install -g @angular/cli
```

Verify the install:

```powershell
ng version
```

If `ng` command is not found, restart PowerShell or check your PATH.

## 2) Create a new Angular project

```powershell
ng new my-angular-app
cd my-angular-app
```

The CLI will prompt you to choose:
- Routing (yes/no) — add Angular Router for multi-page navigation
- Stylesheet format (CSS, SCSS, SASS, Less) — choose your preference

Or use flags to skip prompts:

```powershell
ng new my-angular-app --routing --style=scss --skip-git
```

## 3) Start the development server

```powershell
ng serve
# or: ng serve --open  (opens browser automatically)
```

The app will be available at `http://localhost:4200` by default. The server watches for file changes and recompiles automatically.

## 4) Generate components, services, etc.

Angular CLI provides schematics to scaffold code:

```powershell
# generate a new component
ng generate component components/my-component
# short form:
ng g c components/my-component

# generate a service
ng generate service services/my-service
ng g s services/my-service

# generate a module
ng generate module modules/my-module
ng g m modules/my-module

# generate a guard
ng generate guard guards/auth-guard
ng g g guards/auth-guard
```

## 5) Build for production

```powershell
ng build
# output goes to dist/ folder
# or with optimization flags:
ng build --configuration production
```

## 6) Run tests

```powershell
# run unit tests (Jasmine/Karma)
ng test

# run end-to-end tests
ng e2e
```

## 7) Common npm scripts (in package.json)

```json
"scripts": {
  "ng": "ng",
  "start": "ng serve",
  "build": "ng build",
  "test": "ng test",
  "lint": "ng lint"
}
```

Run with `npm run <script>`:

```powershell
npm start          # runs ng serve
npm run build      # builds for production
npm test           # runs tests
```

## 8) Project structure

```
my-angular-app/
├── src/
│   ├── app/                    # application code
│   │   ├── app.component.ts
│   │   ├── app.module.ts
│   │   └── ...
│   ├── assets/                 # static files (images, etc.)
│   ├── styles.css              # global styles
│   ├── main.ts                 # entry point
│   └── index.html              # main HTML
├── angular.json                # Angular CLI config
├── tsconfig.json               # TypeScript config
├── package.json                # npm dependencies
└── README.md
```

## 9) Useful Angular CLI commands

```powershell
ng version                     # show Angular versions
ng help                        # show all available commands
ng serve --port 4300          # serve on a different port
ng generate --help            # list all code generation types
ng build --stats-json         # output bundle analysis
ng lint                        # lint code (if ESLint is configured)
```

## 10) Common troubleshooting

- **`ng` command not found**: Verify Angular CLI is installed globally with `npm list -g @angular/cli`. If missing, reinstall with `npm install -g @angular/cli`.
- **Port 4200 already in use**: Use a different port with `ng serve --port 4300`.
- **Module not found errors**: Run `npm install` to ensure dependencies are installed.
- **Slow compilation**: Angular compilation can be slow on first run. Subsequent changes use hot module reloading and should be faster.
- **PowerShell execution policy**: If `npm` or `ng` scripts don't run, resolve the execution policy issue (see [PowerShell npm.ps1 fix](../PowerShell/NpmCannotBeLoaded.md)).

## 11) Useful resources

- Angular documentation: https://angular.io/docs
- Angular CLI guide: https://angular.io/cli
- Angular tutorial: https://angular.io/tutorial
- Angular style guide: https://angular.io/guide/styleguide
- TypeScript handbook: https://www.typescriptlang.org/docs/

## 12) Quick checklist (copy-ready PowerShell)

```powershell
# install Node.js first if not already done
# (download from https://nodejs.org/)

# install Angular CLI globally
npm install -g @angular/cli

# verify install
ng version

# create a new project
ng new my-angular-app
cd my-angular-app

# start dev server
ng serve --open

# in another terminal, generate a component
ng g c components/my-component

# build for production
ng build
```

---
**Next steps:**  
- Follow the Angular tutorial for a deeper understanding of components, services, routing, and HTTP.
- Install the **Angular Language Service** extension in VS Code for IntelliSense and type checking.
- Explore Angular Material (https://material.angular.io/) for pre-built UI components.
