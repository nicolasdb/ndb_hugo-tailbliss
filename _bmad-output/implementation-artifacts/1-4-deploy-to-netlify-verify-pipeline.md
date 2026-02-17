# Story 1.4: Deploy to Netlify & Verify Pipeline

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **portfolio owner**,
I want the cleaned, branded site deployed to Netlify,
So that the full build/deploy pipeline is proven and I can deploy incrementally from now on.

## Acceptance Criteria

1. **Given** the repo has a clean codebase with design tokens and simplified dark mode
   **When** the deployment is configured
   **Then** the GitHub repo is connected to Netlify

2. **And** `netlify.toml` build command executes successfully on Netlify (must run `pnpm run build`, not just `hugo`)

3. **And** the deployed site loads without CSS errors or broken images

4. **And** the site is accessible at the Netlify URL

5. **And** subsequent pushes to `main` trigger automatic rebuilds

6. **And** the deployed site respects OS dark/light preference correctly

7. **And** the epic milestone is tagged: `v0.1.0-phase0-foundation`

## Tasks / Subtasks

- [ ] Task 1: Fix `netlify.toml` build command (AC: #2) **CRITICAL**
  - [ ] 1.1 Update `netlify.toml` `[build]` command from `hugo --gc --minify` to `pnpm run build`
  - [ ] 1.2 Add `[build.environment]` with `NODE_VERSION = "20"` (Netlify needs Node for pnpm/Vite)
  - [ ] 1.3 Update all context commands (production, split1, deploy-preview, branch-deploy) to use `pnpm run build` or appropriate variant
  - [ ] 1.4 Verify `publish = "public"` is still correct (Hugo outputs to `public/`)

- [ ] Task 2: Connect GitHub repo to Netlify (AC: #1, #5)
  - [ ] 2.1 This is a manual step — user connects repo in Netlify dashboard
  - [ ] 2.2 Verify auto-deploy is enabled for `main` branch
  - [ ] 2.3 Document the Netlify site URL once created

- [ ] Task 3: Verify deployed site (AC: #3, #4, #6)
  - [ ] 3.1 Check deployed URL loads without errors
  - [ ] 3.2 Open DevTools → Console → no CSS 404s or JS errors
  - [ ] 3.3 Verify CSS is loaded (design tokens applied, not unstyled)
  - [ ] 3.4 Test dark mode: toggle OS preference → site theme changes
  - [ ] 3.5 Check no broken images (site-logo.svg, favicon)

- [ ] Task 4: Verify auto-deploy pipeline (AC: #5)
  - [ ] 4.1 Make a trivial commit to `main` (e.g., add comment to hugo.yaml)
  - [ ] 4.2 Push to `main`
  - [ ] 4.3 Verify Netlify triggers automatic rebuild
  - [ ] 4.4 Verify updated site is live after rebuild

- [ ] Task 5: Tag milestone (AC: #7)
  - [ ] 5.1 Tag `v0.1.0-phase0-foundation` on `main` after successful deployment
  - [ ] 5.2 Push tag to remote

- [ ] Task 6: Verify build passes locally (all ACs)
  - [ ] 6.1 Run `pnpm run build` locally — must succeed
  - [ ] 6.2 Run `pnpm run test` — Hugo build must pass

## Dev Notes

### CRITICAL BUG: netlify.toml Missing Vite Build Step

**Current `netlify.toml` (BROKEN for this project):**
```toml
[build]
command = "hugo --gc --minify"
```

**This skips Vite CSS processing entirely.** On Netlify, Hugo runs without `static/css/main.*.css` existing (Vite never generates it), causing either:
- Missing CSS → unstyled site
- Or stale CSS if `static/css/` was committed (it shouldn't be — it's gitignored)

**Fix:**
```toml
[build]
publish = "public"
command = "pnpm run build"

[build.environment]
HUGO_VERSION = "0.148.2"
HUGO_ENV = "production"
NODE_VERSION = "20"
```

`pnpm run build` = `npm run build:css && hugo --minify --gc --cleanDestinationDir` (from package.json). This runs Vite CSS build first, then Hugo.

### Netlify Build Environment Requirements

Netlify needs:
- **Node.js 20+** for pnpm and Vite 7.x
- **pnpm** — Netlify auto-detects from `pnpm-lock.yaml` (if present) or needs `PNPM_FLAGS` env var
- **Hugo Extended 0.148.2+** — set via `HUGO_VERSION` env var

**Check:** Does the repo have `pnpm-lock.yaml`? Netlify uses it to detect pnpm. If only `package-lock.json` exists, Netlify will use npm instead. The `package.json` scripts use `npm run` internally, so either package manager works for the build command.

### netlify.toml Rewrite Plan

**Current file has several contexts that all run bare `hugo` commands.** All must be updated:

```toml
[build]
publish = "public"
command = "pnpm run build"

[build.environment]
HUGO_VERSION = "0.148.2"
HUGO_ENV = "production"
NODE_VERSION = "20"

[context.production.environment]
HUGO_ENABLEGITINFO = "true"

[context.deploy-preview]
command = "pnpm run build"

[context.deploy-preview.environment]
HUGO_VERSION = "0.148.2"
NODE_VERSION = "20"

[context.branch-deploy]
command = "pnpm run build"

[context.branch-deploy.environment]
HUGO_VERSION = "0.148.2"
NODE_VERSION = "20"
```

**Removed:** `split1` and `next` contexts (unused TailBliss remnants). Removed `--buildFuture` and `$DEPLOY_PRIME_URL` from preview commands — those were Hugo flags passed inline; if needed, add them to `hugo.yaml` or a separate preview script.

**Simplified:** deploy-preview and branch-deploy use the same `pnpm run build` — the Hugo production flags are already in `package.json`.

### What the User Must Do Manually

This story has a **manual component** — connecting the GitHub repo to Netlify:
1. Log into Netlify dashboard
2. "Add new site" → "Import existing project" → select GitHub repo
3. Build settings should auto-detect from `netlify.toml`
4. Deploy

The dev agent can prepare all config files but cannot perform the Netlify connection.

### Static CSS Files and .gitignore

**Verify `static/css/` is in `.gitignore`.** If CSS build artifacts are committed, Netlify would serve stale CSS. The correct flow:
1. Netlify runs `pnpm install` (installs Vite, TailwindCSS)
2. Netlify runs `pnpm run build` (Vite generates fresh CSS → Hugo builds)
3. Fresh CSS in every deploy

### Redirects

Current `netlify.toml` has a redirect rule:
```toml
[[redirects]]
from = "/npmjs/*"
to = "/npmjs/"
status = 200
```
This is a TailBliss remnant. **Remove it** — this portfolio has no `/npmjs/` route.

### File Changes Summary

**Modify:**
- `netlify.toml` — rewrite build commands, add Node version, remove stale contexts and redirects

**No changes:**
- `package.json` — `pnpm run build` script already correct
- `hugo.yaml` — already configured
- CSS/templates — unchanged

**Manual:**
- Connect repo to Netlify (user action)
- Tag `v0.1.0-phase0-foundation` after successful deployment

### Project Structure Notes

- Deployment aligns with architecture D3.2: "Netlify only, pnpm run build on push to main"
- Branch strategy per D3.1: this is the last story in Epic 1, tag milestone after
- No conflict with any existing files

### References

- [Source: _bmad-output/planning-artifacts/epics.md § Story 1.4: Deploy to Netlify & Verify Pipeline]
- [Source: _bmad-output/planning-artifacts/epics.md § Party Mode Decisions — Netlify deployment moved to Epic 1]
- [Source: netlify.toml — current build config (BROKEN: missing Vite step)]
- [Source: package.json lines 25 — `"build": "npm run build:css && hugo --minify --gc --cleanDestinationDir"`]
- [Source: _bmad-output/planning-artifacts/architecture.md § D3.2: CI/CD]

## Previous Story Intelligence

### Learnings from Story 1.2 (Design Tokens & CSS Pipeline)

- Vite processes `assets/css/main.css` → outputs to `static/css/main.{hash}.css`
- Hugo reads from `static/css/` — if Vite hasn't run, CSS is missing
- Build chain: `pnpm run build` = Vite CSS + Hugo (correct order)
- CSS output: `main.6kt1YpOn.css` (56.89 kB, 11.00 kB gzipped)

### Learnings from Story 1.3 (Dark Mode Simplification)

- Story 1.3 is ready-for-dev (not yet implemented) — dark mode may still have toggle
- If 1.3 is done before 1.4: darkmode.js deleted, no JS errors expected on deploy
- If 1.3 is NOT done before 1.4: toggle still exists, darkmode.js still loaded — deploy will still work, just with toggle visible

### Code Patterns Established

- Build validation: `pnpm run test` before marking done
- `pnpm run build` is the canonical full build command

## Git Intelligence Summary

**Expected commits:** `"story1.4 completed"`

**Branch:** `phase0/1-4-deploy-to-netlify` from `main`

**Milestone tag:** `v0.1.0-phase0-foundation` — this is Epic 1's completion tag per epics.md.

## Testing Requirements

### Local Build Validation

```bash
pnpm run build    # Full build must succeed locally before deploying
pnpm run test     # Hugo validation must pass
```

### Deployment Validation

1. Netlify build log shows Vite CSS build + Hugo build (not just Hugo)
2. Deployed site loads CSS (not unstyled)
3. DevTools Console: no 404s, no JS errors
4. OS dark/light preference works on deployed site
5. Subsequent push to `main` triggers auto-rebuild
6. `git tag v0.1.0-phase0-foundation` exists on `main`

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
