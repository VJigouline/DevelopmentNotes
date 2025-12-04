**Add Routes (Angular)**

This note explains how to add and use routes in an Angular application. It covers creating a routing module, defining routes, linking, programmatic navigation, route parameters, child routes, lazy loading, guards/resolvers, and useful CLI commands and tips.

**Prerequisites**: Angular CLI project created. Basic knowledge of components and modules.

**Create App Routing Module**: create a dedicated routing module and import it into `AppModule`.

Example `app-routing.module.ts`:

```ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { NotFoundComponent } from './not-found/not-found.component';

const routes: Routes = [
	{ path: '', redirectTo: 'home', pathMatch: 'full' },
	{ path: 'home', component: HomeComponent },
	{ path: 'about', component: AboutComponent },
	{ path: 'products/:id', loadChildren: () => import('./product/product.module').then(m => m.ProductModule) },
	{ path: '**', component: NotFoundComponent },
];

@NgModule({
	imports: [RouterModule.forRoot(routes, { useHash: false })],
	exports: [RouterModule]
})
export class AppRoutingModule {}
```

Register in `app.module.ts`:

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
	declarations: [AppComponent],
	imports: [BrowserModule, AppRoutingModule],
	bootstrap: [AppComponent]
})
export class AppModule {}
```

**Router outlet**: include the primary outlet in a template (typically `AppComponent`):

```html
<router-outlet></router-outlet>
```

**Linking & active links**
- `routerLink` directive binds a route to an anchor or element.
- `routerLinkActive` applies classes when the link is active.

Examples:

```html
<a routerLink="/home">Home</a>
<a routerLink="/about" routerLinkActive="active">About</a>
<a [routerLink]="['/products', product.id]">Product</a>
```

Advanced active options:

```html
<a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">Root</a>
```

**Programmatic navigation**

```ts
import { Router } from '@angular/router';

constructor(private router: Router) {}

goToDetails(id: string) {
	this.router.navigate(['/products', id]);
	// or
	// this.router.navigateByUrl('/products/' + id);
}
```

**Route parameters**

Define a parameter in route: `{ path: 'users/:id', component: UserComponent }`.
Access with `ActivatedRoute`:

```ts
import { ActivatedRoute } from '@angular/router';

constructor(private route: ActivatedRoute) {}

ngOnInit() {
	// snapshot (one-time)
	const id = this.route.snapshot.paramMap.get('id');

	// subscribe (reactive)
	this.route.paramMap.subscribe(params => {
		const id = params.get('id');
		// load data
	});
}
```

Query params and fragments:

```ts
this.router.navigate(['/search'], { queryParams: { q: 'term' }, fragment: 'top' });

// read
this.route.queryParamMap.subscribe(q => console.log(q.get('q')));
```

**Child / nested routes**

Use `children` in a route and place a secondary `<router-outlet>` inside the parent component template.

```ts
const routes: Routes = [
	{ path: 'admin', component: AdminComponent, children: [
		{ path: '', component: AdminHomeComponent },
		{ path: 'users', component: AdminUsersComponent },
	]}
];
```

**Lazy loading feature modules**

Prefer lazy loading for larger features. Use the dynamic import style:

```ts
{ path: 'feature', loadChildren: () => import('./feature/feature.module').then(m => m.FeatureModule) }
```

Inside `FeatureModule` you typically have its own `FeatureRoutingModule`.

CLI to generate a lazy feature with route automatically:

```sh
ng generate module feature --route feature --module app.module
```

**Guards and resolvers (brief)**

- Protect routes with guards: `canActivate`, `canLoad`, etc.
- Resolve data before route activation with `resolve` and a resolver service.

Example guard usage:

```ts
{ path: 'admin', component: AdminComponent, canActivate: [AuthGuard] }
```

Example resolver:

```ts
{ path: 'profile/:id', component: ProfileComponent, resolve: { profile: ProfileResolver } }

// in component
this.route.data.subscribe(d => this.profile = d['profile']);
```

**Wildcard route / 404**

Place the wildcard last:

```ts
{ path: '**', component: NotFoundComponent }
```

**Helpful CLI commands**

- Generate routing module when creating app: `ng generate module app-routing --flat --module=app`
- Generate component: `ng generate component about`
- Generate lazy module with route: `ng generate module admin --route admin --module app.module`

**Tips & best practices**
- Keep routes small and logical; group feature routes inside feature modules.
- Use lazy loading for large features to improve initial bundle size.
- Use `preloadingStrategy: PreloadAllModules` in `forRoot` to preload lazy modules after initial load if desired.
- Set `<base href="/">` in `index.html` for normal (PathLocationStrategy) routing.
- Use `enableTracing: true` temporarily to debug route events: `RouterModule.forRoot(routes, { enableTracing: true })`.
- Prefer `Router` for programmatic navigation and `ActivatedRoute` for reading params/query params.

**References / Quick checklist**
- Ensure `AppRoutingModule` is imported into `AppModule`.
- Ensure `<router-outlet>` exists in app template.
- Confirm lazy-loaded modules export a routed module (a routing module with `forChild`).
- Put wildcard route last.

---
Updated: concise guide to add and use routes in Angular applications.

