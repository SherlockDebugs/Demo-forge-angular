# Testing forge-angular

## Overview
This skill covers how to locally build and test the `@tylertech/forge-angular` library and its demo application.

## Prerequisites
- Node.js installed (check `.nvmrc` or `engines` field if present)
- No external credentials or secrets needed for local dev

## Build & Serve

### Library build
```bash
npm run build
```
This compiles the library from `projects/forge-angular/` into `dist/forge-angular/` using ng-packagr in partial compilation mode.

### Demo app (production build)
```bash
npm run build:demo
```
Builds the demo app to `dist/forge-angular-demo/`. Note: the Forge library chunk is ~1.7MB, so bundle budgets in `angular.json` must accommodate this.

### Dev server
```bash
npm start
```
Builds the library first, then serves the demo app at `http://localhost:4200` with live reload.

Alternatively, if the library is already built:
```bash
npx ng serve --port 4200
```

## Demo App Structure
- **Home** (`/`): Welcome page with library description
- **Components** (`/component/{name}`): Individual Forge component demos (accordion, autocomplete, button, checkbox, dialog, etc.) — all lazy-loaded
- **Examples** (`/example/{name}`): Integration examples including:
  - `reactive-form`: Demonstrates ReactiveFormsModule + Forge form components (text fields, switches, date/time pickers, radio buttons, selects)
  - `dialog-service`: Dynamic dialog creation via Angular DI
  - `table`: Table with sorting/pagination
  - `expansion-panel`, `toolbar-example`, `two-column-grid`

## Key Testing Flows

1. **Verify Angular version**: Open DevTools Elements panel → check `ng-version` attribute on `<app-root>` element
2. **Lazy-loaded routes**: Click any component in the sidenav → verify the route loads without console errors
3. **Forms integration**: Navigate to `/example/reactive-form` → type in a text field → verify the reactive form model JSON at the top updates in real-time
4. **Console errors**: Check browser console — expect only:
   - "Lit is in dev mode" warning (from Forge web components, normal in dev)
   - "Angular is running in development mode" (normal in dev)
   - Zero actual errors

## Proxy Regeneration
To regenerate Angular proxy components from Forge metadata:
```bash
npm run generate-proxies
```
This runs `ng generate @tylertech/forge-schematics:custom-elements` using the config in `generate-proxies.json`.

## Linting
There is no `ng lint` target configured. ESLint can be run directly:
```bash
npx eslint "projects/**/*.ts"
```
Note: Auto-generated proxy files may have pre-existing lint issues (trailing spaces, missing newlines) that are artifacts of the schematics generator.

## Common Issues
- **Budget errors on demo build**: The Forge library is large (~1.7MB). If `npm run build:demo` fails with budget errors, check the `budgets` config in `angular.json` under the demo app's production configuration.
- **Sass deprecation warnings**: The table example uses `@import` which is deprecated in Dart Sass 3.0. This is a build-time warning only and does not affect functionality.
- **Husky/commitlint**: The repo uses Husky with commitlint enforcing Conventional Commits format. Commit messages must follow `type(scope): subject` format.

## Devin Secrets Needed
None — the library and demo app run entirely locally with no external service dependencies.
