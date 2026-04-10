# Architecture

**Analysis Date:** 2026-04-10

## Pattern Overview

**Overall:** WordPress block plugin with modular plugin-loader architecture

**Key Characteristics:**
- Block lifecycle is split across editor registration, editor rendering, save serialization, and frontend hydration in `src/index.js`, `src/edit.js`, `src/save.js`, and `src/view.js`.
- tsParticles capabilities are modeled as catalog entries (name/description/group/load) and loaded on demand by category handlers in `src/plugins/**/*.js`.
- A central orchestrator (`src/load.js`) owns plugin discovery and dispatch, while category modules own concrete dynamic imports.

## Layers

**WordPress Bootstrap Layer:**
- Purpose: Register the plugin and block with WordPress core.
- Location: `wordpress-particles.php`
- Contains: `add_action('init', ...)`, `register_block_type(__DIR__ . '/build')`, translation binding.
- Depends on: WordPress PHP APIs (`register_block_type`, `wp_set_script_translations`).
- Used by: WordPress runtime during plugin initialization.

**Block Registration Layer:**
- Purpose: Bind block metadata to edit/save implementations.
- Location: `src/index.js`, `src/block.json`
- Contains: `registerBlockType(metadata.name, { edit, save })`, block schema, attributes, script/style entry declarations.
- Depends on: `@wordpress/blocks`, local edit/save modules.
- Used by: Gutenberg editor script loading.

**Editor UI Layer:**
- Purpose: Render inspector controls and block preview container for authoring.
- Location: `src/edit.js`, `src/editor.scss`
- Contains: `InspectorControls`, `TextControl`, `TextareaControl`, dynamic toggle generation from plugin catalog.
- Depends on: `@wordpress/components`, `@wordpress/block-editor`, `@wordpress/i18n`, plugin loader APIs from `src/load.js`.
- Used by: Gutenberg editor when block is inserted/edited.

**Serialization Layer:**
- Purpose: Produce persisted block markup with runtime configuration encoded in data attributes.
- Location: `src/save.js`
- Contains: Saved `<div>` with `id`, inline `style`, `data-options`, `data-plugins`.
- Depends on: `@wordpress/block-editor`, loader APIs from `src/load.js`, `@tsparticles/engine`.
- Used by: Gutenberg post serialization into `post_content`.

**Frontend Runtime Layer:**
- Purpose: Rehydrate saved blocks in the browser and instantiate particle canvases.
- Location: `src/view.js`
- Contains: DOM query for block wrappers, plugin set aggregation, loader invocation, per-element `tsParticles.load`.
- Depends on: `@tsparticles/engine`, `loadWordpressParticles` from `src/load.js`.
- Used by: Visitors loading pages that contain the block.

**Plugin Catalog + Loading Layer:**
- Purpose: Define all loadable tsParticles modules and execute the correct dynamic import for each selected module.
- Location: `src/load.js`, `src/utils.js`, `src/plugins/bundles.js`, `src/plugins/effects.js`, `src/plugins/interactions/*.js`, `src/plugins/movers.js`, `src/plugins/paths.js`, `src/plugins/plugins/*.js`, `src/plugins/presets.js`, `src/plugins/shapes.js`, `src/plugins/updaters.js`
- Contains: Catalog factories (`get*`), handler functions (`handle*`), generic helpers (`transformLoadableObject`, `handlePlugin`).
- Depends on: Dynamic imports of `@tsparticles/*` packages and `tsparticles` bundle.
- Used by: `src/edit.js`, `src/save.js`, and `src/view.js`.

## Data Flow

**Block Authoring and Rendering Flow:**

1. WordPress initializes the plugin via `tsparticles_block_init` in `wordpress-particles.php`, registering block assets from `build/`.
2. Gutenberg loads the editor entry `src/index.js`, which registers the block using metadata from `src/block.json`.
3. The edit component in `src/edit.js` renders controls and updates block attributes (`width`, `height`, `id`, `options`, and plugin booleans).
4. Plugin toggles are generated from `getAllPlugins()` in `src/load.js`, so UI options mirror loader catalog entries.
5. The save function in `src/save.js` serializes attributes into DOM attributes (`data-options`, `data-plugins`) in saved markup.
6. On frontend load, `src/view.js` scans `.wp-block-tsparticles-tsparticles-wp-block`, builds a unique plugin set, runs `loadWordpressParticles`, and loads one tsParticles instance per block element.

**Plugin Resolution Flow:**

1. `getAllPlugins(attributes?)` in `src/load.js` concatenates category catalogs from all plugin modules.
2. When `attributes` are provided, `getAllPlugins` filters catalog items by boolean block attributes in `src/block.json`.
3. `loadWordpressParticles(engine, plugins)` iterates selected plugin names.
4. For each plugin name, category handlers are invoked in sequence (`handleBundles` → `handleEffects` → `handleInteractions` → ... → `handleUpdaters`) in `src/load.js`.
5. The first matching handler loads the package through `handlePlugin` in `src/utils.js` and short-circuits handler evaluation for that plugin.

**State Management:**
- Authoring state is block attribute state managed by Gutenberg (`attributes` + `setAttributes`) in `src/edit.js`.
- Runtime state is DOM-driven: saved values are read from `el.dataset.options` and `el.dataset.plugins` in `src/view.js`.
- No separate global state store is implemented.

## Key Abstractions

**Loadable Plugin Descriptor:**
- Purpose: Represent a selectable tsParticles capability in a uniform shape.
- Examples: Objects in `src/plugins/bundles.js`, `src/plugins/presets.js`, `src/plugins/shapes.js`.
- Pattern: Descriptor object with `name`, `description`, `group`, and async `load(engine)`; exported as UI-safe objects via `transformLoadableObject` in `src/utils.js`.

**Category Handler Contract:**
- Purpose: Encapsulate plugin matching/loading per domain (bundles, effects, interactions, etc.).
- Examples: `handleBundles` in `src/plugins/bundles.js`, `handleInteractions` in `src/plugins/interactions/index.js`, `handleUpdaters` in `src/plugins/updaters.js`.
- Pattern: `handleX(pluginName, engine)` delegates to `handlePlugin(source, pluginName, engine)` and returns boolean loaded status.

**Catalog Aggregator:**
- Purpose: Provide a single API for plugin listing and plugin loading.
- Examples: `getAllPlugins` and `loadWordpressParticles` in `src/load.js`.
- Pattern: Composite orchestration over independent category modules.

## Entry Points

**WordPress Plugin Entry Point:**
- Location: `wordpress-particles.php`
- Triggers: WordPress `init` hook.
- Responsibilities: Register block type and translation support.

**Block Editor Entry Point:**
- Location: `src/index.js`
- Triggers: `editorScript` declared in `src/block.json` and built into `build/index.js`.
- Responsibilities: Register block metadata and bind `edit`/`save` implementations.

**Frontend Script Entry Point:**
- Location: `src/view.js`
- Triggers: `viewScript` declared in `src/block.json` and built into `build/view.js`.
- Responsibilities: Discover saved block elements, load required plugins, instantiate particles.

**Loader Entry Point:**
- Location: `src/load.js`
- Triggers: Calls from `src/edit.js`, `src/save.js`, and `src/view.js`.
- Responsibilities: Aggregate plugin descriptors, filter selected plugins, and dispatch load handlers.

## Error Handling

**Strategy:** Promise-based async flow without explicit try/catch boundaries.

**Patterns:**
- Dynamic import failures propagate from `load` functions in `src/plugins/**/*.js`.
- JSON parsing uses direct `JSON.parse(...)` calls in `src/edit.js`, `src/save.js`, and `src/view.js`; malformed JSON throws and bubbles.

## Cross-Cutting Concerns

**Logging:** Console logging is only present in version automation script `scripts/postversion.js`; runtime block code in `src/**/*.js` does not implement application logging.
**Validation:** Attribute types/defaults are declared in `src/block.json`; runtime option validation before `JSON.parse` is not implemented in `src/edit.js`, `src/save.js`, or `src/view.js`.
**Authentication:** Not applicable for this plugin architecture; no auth layer is implemented in `src/` or `wordpress-particles.php`.

---

*Architecture analysis: 2026-04-10*
