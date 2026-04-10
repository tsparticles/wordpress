# Codebase Concerns

**Analysis Date:** 2026-04-10

## Tech Debt

**Plugin registry duplication across multiple files:**
- Issue: Plugin identifiers are manually duplicated in dependency lists, block attributes, and loader maps, creating high drift risk when adding/removing a plugin.
- Files: `package.json`, `src/block.json`, `src/load.js`, `src/plugins/bundles.js`, `src/plugins/effects.js`, `src/plugins/interactions/index.js`, `src/plugins/movers.js`, `src/plugins/paths.js`, `src/plugins/plugins/index.js`, `src/plugins/presets.js`, `src/plugins/shapes.js`, `src/plugins/updaters.js`
- Impact: Missing or stale attributes/dependencies silently produce non-working toggles and runtime plugin load failures.
- Fix approach: Generate `src/block.json` plugin attributes from a single plugin registry source and validate parity in CI.

**Post-version automation contains conflicting update paths:**
- Issue: Version bump script updates `src/block.json` twice using different path strategies, including a relative write path that is not anchored to the script directory.
- Files: `scripts/postversion.js`
- Impact: Version sync can target an unintended file path (`../src/block.json` from repository root), causing inconsistent release metadata.
- Fix approach: Resolve paths with `path.resolve(__dirname, ...)`, update each target once, and fail on callback errors.

**Large generated build tree committed to repository:**
- Issue: Built assets are committed as dozens of chunk files, increasing maintenance overhead and review noise.
- Files: `build/*.js`, `build/index.js`, `build/view.js`, `build/block.json`
- Impact: Large diffs obscure functional changes and make regression review harder.
- Fix approach: Keep generated artifacts if required for WordPress distribution, but gate via reproducible build checks and artifact-diff validation.

## Known Bugs

**Only first requested plugin is loaded during runtime registration:**
- Symptoms: When multiple plugin toggles are enabled, runtime loads only the first matched plugin/bundle.
- Files: `src/load.js`
- Trigger: `loadWordpressParticles` returns from the entire function on first successful `handle*` call inside the loop.
- Workaround: Use `bundle-full` in `src/block.json` defaults so features exist through full bundle load.

**Editor preview ignores custom block id during particle load:**
- Symptoms: Changing the block `id` attribute does not align with the target used by the editor preview loader.
- Files: `src/edit.js`
- Trigger: Preview calls `tsParticles.load('tsparticles', ...)` while rendered element id is `attributes.id || 'tsparticles'`.
- Workaround: Keep default id (`tsparticles`) in editor usage.

**Block save path performs side effects and can fail on invalid options JSON:**
- Symptoms: Save/render pipeline can throw when `options` is malformed JSON and can trigger runtime side effects from serialization code.
- Files: `src/save.js`, `src/view.js`, `src/edit.js`
- Trigger: Direct `JSON.parse(attributes.options)` and `JSON.parse(el.dataset.options)` without guard/validation.
- Workaround: Manually validate JSON before saving block options.

## Security Considerations

**Unvalidated JSON parsing from persisted content:**
- Risk: Malformed or oversized options payload in block attributes can trigger runtime exceptions and frontend denial-of-render for affected pages.
- Files: `src/edit.js`, `src/save.js`, `src/view.js`
- Current mitigation: Not detected (no try/catch or schema validation around parse operations).
- Recommendations: Validate against a JSON schema before save, wrap parses in guarded error handling, and surface editor-side validation errors.

**Dependency channel uses beta versions for all core runtime packages:**
- Risk: Broad `^4.0.0-beta.11` ranges can introduce unstable runtime behavior through transitive updates.
- Files: `package.json`
- Current mitigation: Renovate config present (`renovate.json`) for update management.
- Recommendations: Pin exact versions for release branches and add compatibility smoke tests for plugin loading.

## Performance Bottlenecks

**Default runtime loads full tsParticles bundle:**
- Problem: Default block config enables `bundle-full`, loading a heavy bundle even when only small feature sets are needed.
- Files: `src/block.json`, `src/plugins/bundles.js`
- Cause: `bundle-full` default is `true` and dynamic import loads `tsparticles` full loader.
- Improvement path: Default to slim/basic and progressively enable specific plugins.

**Sequential plugin initialization and per-element sequential loads:**
- Problem: Plugin registration and element initialization are awaited serially, increasing time-to-render with many enabled plugins/blocks.
- Files: `src/load.js`, `src/view.js`
- Cause: `for ... await` style flow across plugin handlers and block elements.
- Improvement path: Batch non-dependent loads with `Promise.all` and keep deterministic ordering only where required.

## Fragile Areas

**Translation handle/textdomain mismatch across PHP and block metadata:**
- Files: `wordpress-particles.php`, `src/block.json`
- Why fragile: Script translation is set for `tsparticles-block-script` with text domain `tsparticles-block`, while block metadata text domain is `wordpress-particles`.
- Safe modification: Align script handle and text domain to one canonical value used by both `wp_set_script_translations` and block metadata.
- Test coverage: No automated tests detected for localization registration paths.

**Generated metadata and source metadata drift risk:**
- Files: `src/block.json`, `build/block.json`, `scripts/postversion.js`
- Why fragile: Version and attribute changes pass through manual edits + script rewrites + generated build output.
- Safe modification: Treat `src/block.json` as source of truth and enforce post-build consistency checks in CI.
- Test coverage: No metadata parity checks detected in `.github/workflows/nodejs.yml`.

## Scaling Limits

**Many-block pages with rich plugin sets:**
- Current capacity: Not explicitly bounded; runtime initializes each block instance in sequence on `DOMContentLoaded`.
- Limit: Render startup time scales linearly with number of blocks and enabled plugin complexity.
- Scaling path: Parallelize independent initialization, avoid full bundle defaults, and cache loaded plugin modules across instances.

## Dependencies at Risk

**`@tsparticles/*` beta ecosystem:**
- Risk: Pre-release APIs and behavior can shift frequently; broad semver ranges amplify unpredictable updates.
- Impact: Frontend/runtime regressions in editor preview and live rendering.
- Migration plan: Lock stable package versions for plugin releases and upgrade through tested batches.

## Missing Critical Features

**Automated validation and regression tests for plugin loading matrix:**
- Problem: No unit/integration tests verify that selected plugin combinations load and render correctly.
- Blocks: Safe refactoring of `src/load.js`, `src/view.js`, and plugin registry modules.

**CI quality gates beyond build:**
- Problem: Pipeline runs build only and does not enforce linting, static checks, or runtime smoke tests.
- Blocks: Early detection of runtime parse failures, localization regressions, and loader logic bugs.

## Test Coverage Gaps

**Runtime loading and JSON parsing paths are untested:**
- What's not tested: Multi-plugin loading behavior, malformed `options` handling, custom id preview behavior, translation handle consistency.
- Files: `src/load.js`, `src/edit.js`, `src/save.js`, `src/view.js`, `wordpress-particles.php`
- Risk: Regressions reach release with no automated detection.
- Priority: High

---

*Concerns audit: 2026-04-10*
