# Creating Shareable Angular Components

This guide explains how to create reusable Angular components, package them as a library, and distribute them across multiple applications using npm, monorepos, or local dependencies.

## Overview: Three approaches

1. **npm library** — Package as a published npm package (public or private registry)
2. **Monorepo** — Manage multiple apps + shared library in one repository (Nx, Yarn Workspaces)
3. **Local npm link** — Link a local library path during development

## Approach 1: Create an Angular library and publish to npm

### Step 1: Generate a library workspace

```powershell
ng new my-workspace --create-application=false
cd my-workspace
ng generate library my-shared-components
```

This creates:
- `projects/my-shared-components/` — library source code
- `projects/my-shared-components/src/public-api.ts` — public exports
- `projects/my-shared-components/ng-package.json` — library build config

### Step 2: Create reusable components in the library

**projects/my-shared-components/src/lib/button/button.component.ts**:

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'lib-button',
  template: `
    <button 
      [class]="'btn btn-' + type"
      [disabled]="disabled"
      (click)="onClick.emit()">
      {{ label }}
    </button>
  `,
  styles: [`
    .btn {
      padding: 10px 20px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-size: 14px;
      font-weight: 500;
    }
    .btn-primary {
      background-color: #007bff;
      color: white;
    }
    .btn-primary:hover {
      background-color: #0056b3;
    }
    .btn:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class LibButtonComponent {
  @Input() label: string = 'Click me';
  @Input() type: 'primary' | 'secondary' = 'primary';
  @Input() disabled: boolean = false;
  @Output() onClick = new EventEmitter<void>();
}
```

### Step 3: Create a library module

**projects/my-shared-components/src/lib/shared-components.module.ts**:

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { LibButtonComponent } from './button/button.component';
import { LibCardComponent } from './card/card.component';

@NgModule({
  imports: [CommonModule],
  declarations: [LibButtonComponent, LibCardComponent],
  exports: [LibButtonComponent, LibCardComponent]
})
export class SharedComponentsModule { }
```

### Step 4: Export from public API

**projects/my-shared-components/src/public-api.ts**:

```typescript
export * from './lib/shared-components.module';
export * from './lib/button/button.component';
export * from './lib/card/card.component';
```

### Step 5: Build the library

```powershell
ng build my-shared-components
```

Output is in `dist/my-shared-components/`.

### Step 6: Publish to npm

**Update package.json** (`projects/my-shared-components/package.json` or root):

```json
{
  "name": "@mycompany/shared-components",
  "version": "1.0.0",
  "description": "Shared Angular components library",
  "repository": {
    "type": "git",
    "url": "https://github.com/mycompany/shared-components.git"
  },
  "keywords": ["angular", "components", "shared"],
  "author": "Your Name",
  "license": "MIT"
}
```

Publish to npm:

```powershell
cd dist/my-shared-components
npm publish
# or for a private registry:
npm publish --registry https://registry.npmjs.org/
```

### Step 7: Use the library in other apps

Install in another Angular app:

```powershell
npm install @mycompany/shared-components
```

Import and use:

**app.module.ts**:

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { SharedComponentsModule } from '@mycompany/shared-components';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, SharedComponentsModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

**app.component.html**:

```html
<lib-button [label]="'Click me'" (onClick)="handleClick()"></lib-button>
<lib-card [title]="'My Card'"></lib-card>
```

## Approach 2: Monorepo with Nx (recommended for multiple apps)

Nx provides a powerful monorepo structure for managing multiple applications and shared libraries.

### Step 1: Create an Nx workspace

```powershell
npx create-nx-workspace@latest my-monorepo --preset=angular
cd my-monorepo
```

### Step 2: Create a shared library

```powershell
nx generate @nx/angular:library shared-components
```

This creates `libs/shared-components/` with:
- `src/lib/` — library source
- `src/index.ts` — public exports
- `project.json` — Nx configuration

### Step 3: Add components to the library

**libs/shared-components/src/lib/button/button.component.ts**:

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'shared-button',
  template: `<button (click)="onClick.emit()">{{ label }}</button>`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ButtonComponent {
  @Input() label: string = 'Click';
  @Output() onClick = new EventEmitter<void>();
}
```

### Step 4: Export from the library

**libs/shared-components/src/index.ts**:

```typescript
export * from './lib/shared-components.module';
export * from './lib/button/button.component';
```

### Step 5: Create multiple applications

```powershell
nx generate @nx/angular:application app1
nx generate @nx/angular:application app2
```

### Step 6: Use the shared library in apps

**apps/app1/src/app/app.module.ts**:

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { SharedComponentsModule } from '@shared-components';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, SharedComponentsModule],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Step 7: Build and run

```powershell
# build shared library
nx build shared-components

# build app1
nx build app1

# serve app1
nx serve app1
```

## Approach 3: Local npm link (development)

Useful for local development before publishing to npm.

### Step 1: Create a library project

```powershell
ng new my-library
cd my-library
ng generate library my-components
```

### Step 2: Link the library locally

```powershell
cd dist/my-components
npm link
```

This registers the library globally for your machine.

### Step 3: Link in your application

In another Angular app:

```powershell
npm link my-components
```

### Step 4: Use the linked library

Import and use as normal:

```typescript
import { MyComponentsModule } from 'my-components';
```

### Step 5: Unlink when done

```powershell
npm unlink my-components
npm unlink -g my-components
```

## Best practices for shareable components

✅ **Do:**
- Use a clear naming convention: `lib-button`, `shared-button`, or a custom prefix.
- Export a module and individual components from `public-api.ts`.
- Document all `@Input()` and `@Output()` properties with JSDoc.
- Version your library using semantic versioning (MAJOR.MINOR.PATCH).
- Include a `README.md` with usage examples.
- Test components thoroughly before publishing.
- Use `ChangeDetectionStrategy.OnPush` for performance.
- Provide a peer dependency on `@angular/core` (don't bundle it).

❌ **Don't:**
- Include hard-coded business logic in shared components.
- Depend on global state or services that are app-specific.
- Bundle Angular as a production dependency (use peerDependencies).
- Forget to update the version number when publishing.
- Break backward compatibility without a major version bump.

## Example: Publishing a scoped package

Scoped packages are namespaced (e.g., `@mycompany/button-library`) and useful for private or organizational packages.

**package.json**:

```json
{
  "name": "@mycompany/button-library",
  "version": "1.0.0",
  "description": "Reusable button component library",
  "license": "MIT",
  "publishConfig": {
    "access": "restricted"
  }
}
```

Publish:

```powershell
npm publish --access restricted
```

Install:

```powershell
npm install @mycompany/button-library
```

## Library documentation structure

When sharing components, include:

```
my-shared-components/
├── README.md                    # Overview, usage, examples
├── CHANGELOG.md                 # Version history
├── projects/my-shared-components/
│   ├── src/
│   │   ├── lib/
│   │   │   ├── button/
│   │   │   ├── card/
│   │   │   └── ...
│   │   └── public-api.ts
│   ├── ng-package.json
│   └── package.json
└── .github/
    └── ISSUE_TEMPLATE/          # GitHub issue templates
```

## Testing shareable components

**button.component.spec.ts**:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { LibButtonComponent } from './button.component';

describe('LibButtonComponent', () => {
  let component: LibButtonComponent;
  let fixture: ComponentFixture<LibButtonComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ LibButtonComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(LibButtonComponent);
    component = fixture.componentInstance;
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should emit onClick event', () => {
    spyOn(component.onClick, 'emit');
    component.onClick.emit();
    expect(component.onClick.emit).toHaveBeenCalled();
  });
});
```

Run tests:

```powershell
ng test my-shared-components
```

## Useful resources

- Angular library guide: https://angular.io/guide/creating-libraries
- ng-packagr: https://ng-packagr.github.io/
- Nx monorepo: https://nx.dev/
- npm scoped packages: https://docs.npmjs.com/about/scoped-packages
- Semantic versioning: https://semver.org/

---
**Quick decision guide:**
- **Single library, multiple apps in same org?** → Use a monorepo (Nx or Yarn Workspaces)
- **Library for public use?** → Publish to npm
- **Developing locally before publishing?** → Use npm link
- **Private company library?** → Use scoped package on private npm registry
