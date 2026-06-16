# JavaScript / TypeScript / Web

## Deno

### Run Deno tests from the function's own directory, not the project root
**Learning:** Deno 2 resolves `deno.json` (and the imports map) relative to the current working directory, not the test file; running `deno test path/to/test.ts` from the root leaves the subdirectory's imports map unresolved → "Import X not a dependency".
**Why:** Deno 2 changed config resolution to the nearest `deno.json` upward from CWD, not from the entry file.
**How to apply:** Run tests and `deno add` from the directory holding that function's own `deno.json`: `cd supabase/functions/my-fn && deno test test.ts --allow-env --allow-net`. Document it per-function.

## TypeScript

### Fix the TypeScript 6 `baseUrl` deprecation in pnpm monorepos
**Learning:** TS 6.x treats `baseUrl` as error TS5101 and halts before real errors surface; in a pnpm monorepo with `shamefully-hoist=true`, root-hoisted `@types/*` are invisible to package-level `tsconfig.json` unless `typeRoots` is set.
**Why:** `ignoreDeprecations` must match the exact TS5101 string (`"6.0"`); pnpm hoists `@types` to the root `node_modules/@types/`, so a package can't find them without a `typeRoots` hint.
**How to apply:**
```json
{ "compilerOptions": {
    "ignoreDeprecations": "6.0",
    "typeRoots": ["../../node_modules/@types", "./node_modules/@types"] } }
```
Run `tsc --noEmit` and count errors *before* adding `ignoreDeprecations` (it can mask a pre-existing cascade). Don't combine `typeRoots` with an explicit `types: []` unless every listed type is reachable from every `typeRoots` path.

### Shim the global `JSX` namespace when upgrading to `@types/react` 19
**Learning:** `@types/react` 19 removed the global `JSX` namespace (now `React.JSX`); files using `: JSX.Element` fail with TS2503 "Cannot find namespace 'JSX'".
**Why:** The package deliberately scoped JSX types to avoid global pollution; old code breaks silently until type-check.
**How to apply:** Add `src/types/jsx-global.d.ts` (included in tsconfig) re-exporting `React.JSX` members into a global `namespace JSX` (Element, IntrinsicElements, IntrinsicAttributes, LibraryManagedAttributes…). Lower-footprint than migrating every annotation; prefer `React.JSX.Element` in new code.

### Invoke pnpm/tsc via resolved paths when pnpm isn't on PATH
**Learning:** Corepack-managed pnpm may not be on `PATH`, and a workspace-hoisted `tsc` shim in `.bin/` can fail when called as `node .bin/tsc`.
**Why:** Corepack puts pnpm in a versioned cache outside standard PATH; the `.bin/tsc` shim's `#!/usr/bin/env node` resolution is invocation-context-sensitive.
**How to apply:** Call tsc through the package's own install: `node apps/pkg/node_modules/typescript/bin/tsc --noEmit`. Add tooling like the Supabase CLI as a workspace devDependency (`pnpm add -w supabase`) and run it via `pnpm exec`, rather than relying on a global install.

## Vanilla JavaScript (no bundler)

### Use IIFE modules + a single global namespace for no-build browser JS
**Learning:** When ES modules are unavailable (e.g. `file://` triggers CORS on `import`), the IIFE-module pattern with one global namespace object is the correct no-build encapsulation.
**Why:** `import`/`export` over `file://` is CORS-blocked; a single `window.APP = window.APP || {}` gives cross-file access without polluting global scope, with HTML script order defining dependencies.
**How to apply:** Each file starts `window.APP = window.APP || {}`; modules are `APP.X = (function(){ var _priv; return {...}; })()`; entities use constructor+prototype. Load order: constants → utils → subsystems → entities → orchestrator.

### Cap delta-time and reverse-iterate entity arrays in a game loop
**Learning:** In a `requestAnimationFrame` loop, an uncapped `dt` makes physics explode when a backgrounded tab resumes; removing dead entities with forward `splice` corrupts indices.
**Why:** Browsers throttle background tabs → one multi-second `dt` spike on resume; forward `splice` shifts later indices by −1, skipping the next element.
**How to apply:** `const dt = Math.min((t - last)/1000, 0.1);` and iterate removals in reverse: `for (let i = a.length-1; i>=0; i--) if (a[i].dead) a.splice(i,1);`. Use `event.code` (layout-independent) for keys; create `AudioContext` only on a user gesture.

## CSS / layout

### Prefer CSS Grid `1fr auto` over flexbox for sticky footers
**Learning:** A flexbox sticky footer (`flex-column` + `flex:1` + `margin-top:auto`) breaks when a JS framework mutates the wrapper or descendant `margin:auto` interferes; Grid `grid-template-rows: 1fr auto` pins the footer reliably.
**Why:** Flexbox stretch + `margin:auto` interact non-deterministically when frameworks add/mutate attributes on flex children; Grid row sizing is declarative.
**How to apply:**
```css
html { height: 100%; }
body { min-height: 100vh; display: grid; grid-template-rows: 1fr auto; }
```
Keep the footer a direct child of `<body>`, outside any framework-managed wrapper. For a fixed consent banner overlapping the footer, toggle a `body.cookie-pending { padding-bottom: … }` class while it's visible.

### Never construct Tailwind class names dynamically
**Learning:** Tailwind's build scans source for *complete* class strings; any class assembled at runtime (`` `bg-${x}-500` ``) is absent from the generated CSS.
**Why:** Tailwind uses static analysis, not runtime evaluation, to decide which utilities to emit.
**How to apply:** Keep full strings intact and branch: `status === 'error' ? 'bg-red-500' : 'bg-green-500'`; use `cn()` (clsx + tailwind-merge) for conditional logic; extract repeated combinations into components.

## LLM API integration

### Always set `max_tokens` and treat model output as untrusted
**Learning:** The Anthropic API requires `max_tokens` (no default — the call fails without it); more broadly, validate and sanitize LLM output before using it in logic or rendering it as HTML.
**Why:** Missing `max_tokens` is a silent contract violation that throws at runtime; responses can be manipulated via prompt injection or return unexpected shapes even in structured-output modes.
**How to apply:** Always pass `max_tokens`; use schema-constrained/JSON output and validate the shape; sanitize user content before interpolating into prompts; retry `overloaded_error`/429 with exponential backoff (never immediately); log prompt version + model ID + stop reason to replay calls; check `stop_reason === "tool_use"` before assuming completion; never expose API keys client-side — proxy through a backend.

## API security (Node/Express)

### Never use `*` as a CORS origin in production
**Learning:** `Access-Control-Allow-Origin: *` lets any origin make credentialed cross-origin requests, defeating CORS as a boundary.
**Why:** A wildcard removes the very restriction CORS exists to provide.
**How to apply:** Keep an explicit allowlist from env: `cors({ origin: process.env.ALLOWED_ORIGINS?.split(',') ?? [] })`.
