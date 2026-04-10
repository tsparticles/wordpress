# tsParticles WordPress Modernization

## What This Is

This project modernizes the existing `wordpress-particles` plugin so it stays aligned with the evolving tsParticles ecosystem and current WordPress tooling. The focus is to upgrade dependencies to current versions, adopt the tsParticles `4.0.0-beta` line being developed in parallel, and migrate code patterns to modern syntax and conventions. It is for maintainers and users of the existing plugin who need compatibility, maintainability, and forward momentum without breaking current behavior.

## Core Value

Keep the existing WordPress tsParticles block reliable while upgrading it to modern dependencies and syntax so future releases are faster and safer to ship.

## Requirements

### Validated

- ✓ WordPress plugin registers and exposes a Gutenberg block for tsParticles configuration and rendering — existing
- ✓ Block saves particle options/plugins as serialized attributes and rehydrates on frontend pages — existing
- ✓ Dynamic tsParticles module loading is supported through plugin catalogs and handlers — existing

### Active

- [ ] Upgrade project dependencies/toolchain to latest stable-compatible versions
- [ ] Integrate and validate tsParticles `4.0.0-beta` packages used by this repository
- [ ] Migrate source code and build scripts to more modern syntax and patterns without regressions

### Out of Scope

- New end-user product features unrelated to modernization — priority is platform upgrade and compatibility
- Major UX redesign of editor controls — avoid scope expansion during migration

## Context

- Existing brownfield codebase already mapped in `.planning/codebase/`
- Plugin is built with WordPress block tooling (`@wordpress/scripts`) and ships PHP bootstrap + JS runtime
- tsParticles package family is central; this repository tracks beta work and must remain aligned with that evolution
- Existing CI uses Node.js 20 and pnpm workspace tooling; modernization should keep CI green

## Constraints

- **Compatibility**: Preserve existing block behavior in editor/frontend — avoid breaking current users
- **Dependency Strategy**: Update to latest versions with emphasis on tsParticles `4.0.0-beta` line — modernization objective
- **Scope**: Focus on upgrade/migration work first — defer unrelated feature work
- **Quality**: Keep build and packaging workflow functional (`build`, `plugin-zip`, release scripts) — required for distribution

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Prioritize dependency updates as the first milestone objective | Reduces drift and unlocks modern syntax/tooling work | — Pending |
| Target tsParticles `4.0.0-beta` compatibility during this initiative | Repository is tied to ongoing beta development and must track it | — Pending |
| Treat syntax modernization as safe refactoring with behavior parity | Goal is maintainability improvement without feature regression | — Pending |

---
*Last updated: 2026-04-10 after initialization*
