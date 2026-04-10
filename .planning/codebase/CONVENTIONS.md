# Coding Conventions

**Analysis Date:** 2026-04-10

## Naming Patterns

**Files:**
- Use lowercase file names with feature/category nouns for modules (for example `src/plugins/bundles.js`, `src/plugins/updaters.js`, `src/plugins/interactions/external.js`).
- Use `index.js` files as module entry points inside grouped directories (for example `src/index.js`, `src/plugins/interactions/index.js`, `src/plugins/plugins/index.js`).

**Functions:**
- Use `camelCase` for function names and exported API (`getAllPlugins`, `loadWordpressParticles`, `handlePlugin` in `src/load.js` and `src/utils.js`).
- Use `get*` prefix for data providers and `handle*` for dispatch/load handlers (for example `getShapes`/`handleShapes` in `src/plugins/shapes.js`).

**Variables:**
- Use `camelCase` for local variables and parameters (`pluginName`, `groupPlugins`, `attributes` in `src/edit.js`, `src/load.js`).
- Use singular iteration variables with array helpers (`map((t) => ...)`, `for (const plugin of plugins)` across `src/**/*.js`).

**Types:**
- Not applicable in current source: implementation files are JavaScript (`src/**/*.js`) with no TypeScript interfaces/types declared.

## Code Style

**Formatting:**
- Tool used: WordPress Scripts formatter via `wp-scripts format` (`package.json` scripts section).
- Key settings:
  - Tabs for indentation in most JS files (`src/index.js`, `src/edit.js`, `src/plugins/shapes.js`).
  - UTF-8, LF, final newline, trailing whitespace trimming from `.editorconfig`.
  - YAML files use 2-space indentation per `.editorconfig`.

**Linting:**
- Tool used: WordPress Scripts linting via `wp-scripts lint-js` and `wp-scripts lint-style` (`package.json`).
- Key rules: no explicit custom ESLint config detected (`.eslintrc*`/`eslint.config.*` not detected), so keep code compatible with default `@wordpress/scripts` lint rules.

## Import Organization

**Order:**
1. External framework/package imports first (for example `@wordpress/*`, `@tsparticles/*` in `src/edit.js`, `src/index.js`).
2. Internal relative imports second (for example `./load`, `../utils`, `./plugins/easings` in `src/load.js`, `src/plugins/plugins/index.js`).
3. Side-effect style imports last when present (for example `import './editor.scss';` in `src/edit.js`, `import './style.scss';` in `src/index.js`).

**Path Aliases:**
- Not detected. Use relative imports only (`./...`, `../...`) as in `src/**/*.js`.

## Error Handling

**Patterns:**
- Prefer guard clauses and boolean success responses in plugin loading logic instead of exceptions (`src/utils.js` returns `true`/`false`; `src/load.js` short-circuits on successful handler call).
- Existing explicit throws appear in maintenance script callbacks (`throw error` in `scripts/postversion.js`); apply same explicit fail-fast behavior in Node scripts.
- No `try/catch` usage detected in runtime `src/**/*.js`; preserve simple async/await flows and surface failures naturally.

## Logging

**Framework:** console

**Patterns:**
- Runtime/editor code in `src/**/*.js` does not log; keep frontend code free of console logging.
- Build/versioning script logs status with `console.log` after file updates (`scripts/postversion.js`). Restrict logging to tooling scripts.

## Comments

**When to Comment:**
- Use block comments for WordPress API context and lifecycle explanations (`src/index.js`, `src/edit.js`, `src/save.js`).
- Add concise comments for side-effect imports and editor/front-end behavior, following existing pattern in block files.

**JSDoc/TSDoc:**
- Use JSDoc-style blocks for exported block functions and parameters (`Edit` in `src/edit.js`, `save` in `src/save.js`).
- Include `@see` links for WordPress docs where relevant (`src/index.js`, `src/edit.js`, `src/save.js`).

## Function Design

**Size:**
- Keep utility handlers compact and single-purpose (`transformLoadableObject`, `handlePlugin` in `src/utils.js`).
- Feature UI functions can be larger but should encapsulate helper closures for repeated JSX (`getLoadPluginField`, `getLoadPLuginGroup` in `src/edit.js`).

**Parameters:**
- Pass dependency/context objects explicitly (for example `engine`, `attributes`, `setAttributes`, `group`, `localizeFn` across `src/**/*.js`).

**Return Values:**
- Return transformed arrays from `get*` providers (`src/plugins/*.js`).
- Return booleans for handler success/failure in dispatch helpers (`src/utils.js`, `src/load.js`).

## Module Design

**Exports:**
- Use named exports for reusable plugin factories and handlers (`getBundles`, `handleBundles` in `src/plugins/bundles.js`).
- Use default export for block component entry points (`export default function Edit` in `src/edit.js`, `export default function save` in `src/save.js`).

**Barrel Files:**
- Use `index.js` as barrel/aggregator modules for grouped plugin families (`src/plugins/interactions/index.js`, `src/plugins/plugins/index.js`).

---

*Convention analysis: 2026-04-10*
