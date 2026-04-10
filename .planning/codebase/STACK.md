# Technology Stack

**Analysis Date:** 2026-04-10

## Languages

**Primary:**
- JavaScript (ES modules) - Gutenberg block/editor runtime and tsParticles plugin loading in `src/index.js`, `src/edit.js`, `src/save.js`, `src/view.js`, and `src/plugins/*.js`
- PHP 7.0+ - WordPress plugin bootstrap and block registration in `wordpress-particles.php`

**Secondary:**
- SCSS/CSS - Block editor/frontend styling in `src/editor.scss`, `src/style.scss`, generated assets in `build/style-index.css`
- JSON - Block metadata and package/tooling configuration in `src/block.json`, `package.json`, `nx.json`, `typedoc.json`, `renovate.json`
- YAML - Workspace and CI configuration in `pnpm-workspace.yaml` and `.github/workflows/nodejs.yml`

## Runtime

**Environment:**
- Node.js 20 for CI builds (`.github/workflows/nodejs.yml`)
- WordPress runtime (requires at least 5.9) and PHP 7.0+ for plugin execution (`wordpress-particles.php`, `readme.txt`)

**Package Manager:**
- pnpm 10.33.0 declared in `package.json` (`packageManager`)
- Lockfile: present (`pnpm-lock.yaml`)

## Frameworks

**Core:**
- WordPress Block Editor/Gutenberg APIs via `@wordpress/blocks`, `@wordpress/block-editor`, `@wordpress/components`, `@wordpress/i18n` (imported in `src/index.js`, `src/edit.js`, `src/save.js`)
- tsParticles engine via `@tsparticles/engine` with modular loaders (`src/load.js`, `src/plugins/**/*.js`)

**Testing:**
- Not detected (no Jest/Vitest config or test files found)

**Build/Dev:**
- `@wordpress/scripts` for build/start/lint/format/plugin packaging (`package.json` scripts: `build:package`, `start`, `lint:*`, `format`, `plugin-zip`)
- Nx for cached build targets (`nx.json`, `package.json` script `build:nx`)
- Lerna orchestration fallback (`package.json` script `build:lerna`)

## Key Dependencies

**Critical:**
- `@tsparticles/engine` - runtime engine used to load and render particles in editor/view (`src/edit.js`, `src/save.js`, `src/view.js`)
- `tsparticles`, `@tsparticles/all`, `@tsparticles/slim`, `@tsparticles/basic` - bundle-level loading options (`src/plugins/bundles.js`)
- `@wordpress/scripts` - canonical WordPress build and packaging pipeline (`package.json`)

**Infrastructure:**
- `@wordpress/*` packages - block registration, controls, i18n (`src/index.js`, `src/edit.js`, `src/save.js`)
- Extensive `@tsparticles/*` plugin ecosystem (effects/interactions/movers/paths/plugins/presets/shapes/updaters) loaded lazily from `src/plugins/*.js`
- `fs-extra` used for release/version file synchronization in `scripts/postversion.js`

## Configuration

**Environment:**
- No `.env` files detected in repository root
- Runtime behavior is configured by block attributes in `src/block.json` and serialized `data-options`/`data-plugins` attributes from `src/save.js`
- WordPress minimum platform constraints declared in `wordpress-particles.php` and `readme.txt`

**Build:**
- Package/build scripts in `package.json`
- Workspace/build-cache config in `pnpm-workspace.yaml` and `nx.json`
- CI workflow in `.github/workflows/nodejs.yml`
- Editor formatting baseline in `.editorconfig`

## Platform Requirements

**Development:**
- Node.js (CI uses 20) with pnpm and WordPress build tooling (`.github/workflows/nodejs.yml`, `package.json`)
- WordPress-compatible dev environment for block/plugin validation (`src/block.json`, `wordpress-particles.php`)

**Production:**
- WordPress installation with plugin deployment to `/wp-content/plugins/wordpress-particles` as documented in `readme.txt`
- PHP 7.0+ and WordPress 5.9+ (`wordpress-particles.php`, `readme.txt`)

---

*Stack analysis: 2026-04-10*
