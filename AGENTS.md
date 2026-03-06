# AGENTS.md — Contributor Guide for Agentic Coding

This file defines practical rules for coding agents working in this repository.
It is based on the current codebase state (single-file web app + release deploy workflow).

## 1) Project Snapshot

- App type: static single-page game (no framework, no bundler)
- Main runtime file: `index.html` (contains HTML + CSS + JavaScript)
- Docs: `README.md`
- CI/CD workflow: `.github/workflows/vercel-release-deploy.yml`
- Package manager / scripts: **none configured** (`package.json` is absent)

## 2) Canonical Commands

### Local run (development/manual QA)

Use a simple static server from repo root:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

Then open:

- Local: `http://127.0.0.1:8000/index.html`
- LAN: `http://<server-ip>:8000/index.html`

### Build / lint / tests

- Build command: **none configured locally**
- Lint command: **none configured**
- Unit/integration test command: **none configured**
- Single-test command: **not applicable** (no test runner configured)

### Deployment (GitHub + Vercel)

Current workflow is in `.github/workflows/vercel-release-deploy.yml`:

- Trigger: `workflow_dispatch` and `release` (`published`, `released`)
- Release guard: non-prerelease and tag starts with `v`
- Deploy commands used by workflow:

```bash
npx --yes vercel@latest build --prod --token "$VERCEL_TOKEN"
npx --yes vercel@latest deploy --prebuilt --prod --yes --token "$VERCEL_TOKEN"
```

Required GitHub Secrets:

- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`

## 3) File & Responsibility Map

- `index.html`
  - `<style>` block: all UI styling/theme variables
  - `<script>` block: all game logic/state/rendering/event handling
- `README.md`
  - User-facing run instructions and gameplay summary
- `.github/workflows/vercel-release-deploy.yml`
  - Release-driven production deployment logic
- `.gitignore`
  - Ignores `.vercel`, logs, opencode session artifacts

## 4) Code Style Guidelines (Observed Conventions)

Follow existing style unless explicitly asked to refactor style globally.

### JavaScript conventions

- Use `const` by default; use `let` only for mutable state.
- Use semicolons consistently.
- Prefer single quotes for string literals.
- Use plain function declarations (`function name() {}`) for core logic.
- Keep enum-like constants as frozen-by-convention objects (e.g. `Difficulty`, `AbilityType`).
- Keep state variables grouped near top of `<script>`.

### Naming conventions

- `camelCase` for functions and variables (`loadUiTheme`, `resetGame`, `spawnEventIfNeeded`).
- `PascalCase` for enum-like objects (`Difficulty`, `AbilityType`, `EventType`).
- `UPPER_SNAKE_CASE` for top-level fixed constants (`GRID_SIZE`, `BASE_SPEED`, `MAX_SPEED`).
- localStorage keys are namespaced with `snake_...` and should stay that way.

### HTML/CSS conventions

- Keep semantic class names short and descriptive (`.app`, `.controls`, `.modal-card`, `.chip`).
- Reuse CSS custom properties (`--bg`, `--panel`, etc.) rather than hardcoding duplicates.
- Preserve current light/dark theme structure via `:root` and `:root[data-theme="light"]`.
- Keep responsive behavior in CSS/media rules; avoid JS for pure layout concerns.

### Formatting

- Preserve current indentation pattern used in each section.
- Avoid unnecessary large reformatting diffs in `index.html`.
- Keep lines reasonably readable; split long expressions only when clarity improves.

## 5) Error Handling & Resilience

- localStorage accesses are expected to be defensive in this project.
  - Existing code uses `try/catch` around non-critical persistence.
  - Keep persistence failures non-fatal to gameplay.
- Do not add throwing behavior for recoverable UI persistence failures.
- Avoid silent catches for critical game logic. If new critical paths can fail, handle explicitly.

## 6) Runtime/Gameplay Change Rules

When modifying gameplay logic:

1. Keep `resetGame()`, `step()`, and `loop()` coherent and in sync.
2. Preserve collision/wrap rules unless requirement says otherwise.
3. Keep HUD sync calls (`syncHUD()`) correct after state transitions.
4. If changing difficulty behavior, update `difficultyConfig()` and ensure selector still applies instantly.
5. If adding persistence keys, prefix with `snake_`.

When modifying visual effects:

1. Keep render path performant (`draw()`, particle loops, requestAnimationFrame loop).
2. Avoid adding expensive per-frame allocations where possible.
3. Test on desktop + mobile interaction paths (keyboard, D-pad, swipe).

## 7) Validation Checklist (No Test Harness Present)

Because automated tests are not configured, validate manually:

1. Start server and load page successfully.
2. Verify Start/Pause/Restart controls.
3. Verify keyboard movement + mobile controls.
4. Verify score updates and best-score persistence.
5. Verify no console-breaking runtime errors during a full game cycle.
6. If deployment-related changes were made, verify workflow YAML syntax and triggers.

Suggested quick smoke command sequence:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
# then open /index.html and perform manual checks
```

## 8) Git/PR Hygiene for Agents

- Keep commits focused and minimal.
- Do not commit `.vercel/` or session dump files.
- Update `README.md` when behavior/controls/deploy process changes.
- Prefer small, reviewable edits over broad rewrites in `index.html`.

## 9) Cursor / Copilot Rules Integration

Checked paths:

- `.cursor/rules/`
- `.cursorrules`
- `.github/copilot-instructions.md`

Current status: **no Cursor/Copilot rule files found in this repository**.

If these files are added later, agents must treat them as higher-priority repo instructions and update this AGENTS.md summary accordingly.

## 10) What Not to Assume

- Do not assume Node-based scripts exist.
- Do not assume lint/test pipelines exist.
- Do not assume module splitting/build tooling is available.
- Do not assume deployment happens on push; follow the workflow file as source of truth.

---

If you introduce tooling (tests/lint/build), update this file with exact commands including a true single-test command.
