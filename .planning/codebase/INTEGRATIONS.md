# External Integrations

**Analysis Date:** 2026-04-10

## APIs & External Services

**WordPress Platform APIs:**
- WordPress Block Registration/Editor APIs - block registration and editor UI integration
  - SDK/Client: `@wordpress/blocks`, `@wordpress/block-editor`, `@wordpress/components`, `@wordpress/i18n` in `src/index.js`, `src/edit.js`, `src/save.js`
  - Auth: Not applicable (executes inside authenticated WordPress admin/editor context)

**Client-Side Rendering Engine:**
- tsParticles package ecosystem - frontend/editor particle rendering and optional feature modules
  - SDK/Client: `@tsparticles/engine` plus dynamically imported `@tsparticles/*` modules in `src/load.js` and `src/plugins/**/*.js`
  - Auth: None

**Translation Infrastructure:**
- WordPress script translation API - localized strings for block UI
  - SDK/Client: `wp_set_script_translations` in `wordpress-particles.php`
  - Auth: Not applicable

## Data Storage

**Databases:**
- WordPress database (indirect via block `post_content` serialization)
  - Connection: Managed by WordPress core (not configured in this repository)
  - Client: WordPress block serialization (`save` output in `src/save.js`)

**File Storage:**
- WordPress plugin filesystem/package assets (`assets/`, `build/`, `languages/` declared in `package.json`)

**Caching:**
- None explicitly configured in repository code

## Authentication & Identity

**Auth Provider:**
- WordPress core authentication/session system
  - Implementation: Plugin hooks into authenticated WordPress admin/editor and frontend runtime via `add_action('init', ...)` in `wordpress-particles.php`

## Monitoring & Observability

**Error Tracking:**
- None detected (no Sentry/Datadog/Bugsnag integration files or imports)

**Logs:**
- Local release script console output only in `scripts/postversion.js`

## CI/CD & Deployment

**Hosting:**
- Target platform is WordPress plugin deployment (documented installation path in `readme.txt`)

**CI Pipeline:**
- GitHub Actions workflow in `.github/workflows/nodejs.yml` running `pnpm install` and `pnpm run build:ci`

## Environment Configuration

**Required env vars:**
- Not detected in source (`process.env`/`import.meta.env` not used)

**Secrets location:**
- Not applicable in repository code; no secret-bearing integration configuration detected

## Webhooks & Callbacks

**Incoming:**
- WordPress hook callbacks on `init`:
  - `tsparticles_block_init` in `wordpress-particles.php`
  - `tsparticles_block_set_script_translations` in `wordpress-particles.php`

**Outgoing:**
- None detected (no HTTP clients, webhook publishers, or remote API calls)

---

*Integration audit: 2026-04-10*
