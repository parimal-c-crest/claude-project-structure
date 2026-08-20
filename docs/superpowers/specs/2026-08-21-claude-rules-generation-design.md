# CLAUDE.md / .claude/rules generation — design spec

**Date:** 2026-08-21
**Scope:** `full-doc/project-docs/` only. `on-demand-module-doc/project-docs/` stays unchanged. Covers **Case A only** (brand-new project, rules derived from already-generated `docs-kit/6-development/` docs). Case B (scanning an existing/legacy codebase's real conventions when no `docs-kit/` exists yet) is explicitly deferred to a later pass.

## Problem

`docs-kit/6-development/` already captures real project standards — coding conventions, folder structure, git workflow, testing strategy, deployment strategy — once generated and approved. But nothing turns that into the Claude Code mechanisms that actually get read automatically: root `CLAUDE.md` (read every session) and `.claude/` (settings, agents, commands — the project's own Claude-specific machinery). Standards sit in `docs-kit/` as reference material a session has to know to go dig up; they don't become part of how every session actually behaves by default.

## Chosen approach

Add a new generation step, right after `6-development`'s early wave is approved, that:
1. Writes **condensed** versions of the three highest-frequency documents into `.claude/rules/` at the real project root.
2. Updates root `CLAUDE.md` to `@import` those three files, plus a short pointer to where the situational docs live in `docs-kit/6-development/` (not duplicated).

### Why split "always-imported" vs "pointer-only"

Claude Code's `@import` mechanism loads the imported file's content into **every session's context automatically**, regardless of whether that session's task needs it. Importing all ten `6-development/` documents into `CLAUDE.md` would mean every session — including one just fixing a typo — pays the token cost of testing strategy, deployment strategy, and CI/CD content it will never touch that turn. Rejected for that reason.

Instead, split by actual frequency of relevance:

- **Always-imported** (`.claude/rules/`, `@import`ed into `CLAUDE.md`): `coding-standards.md`, `folder-structure.md`, `git-workflow.md`. These three matter on nearly every task — naming a variable, placing a file, writing a commit message.
- **Pointer-only** (named in `CLAUDE.md` as a path, never duplicated, never auto-loaded): `6-testing-strategy.md`, `7-deployment-strategy.md`, `10-debugging-guide.md`, `8-containerization.md`, `9-ci-cd.md`. These matter only during specific phases — testing, release, debugging, infra — and are already read directly by the prompt responsible for that phase (`3-generate-tests.md` reads `6-testing-strategy.md`, `10-release/1-release.md` reads `7-deployment-strategy.md`, etc.). No need to duplicate their content anywhere; a one-line pointer in `CLAUDE.md` is enough for a session to know where to look if it needs to.

### Why `.claude/rules/`, not embedded directly in `CLAUDE.md`

Keeps `CLAUDE.md` itself short and scannable (an index, not a document), while each rule stays in its own focused, independently-updatable file — mirrors the source `docs-kit/6-development/` split rather than flattening it into one large file. Matches the existing Claude Code convention of putting project-specific machinery under `.claude/` (`settings.json`, `agents/`, `commands/`), rather than scattering it at repo root.

### Why "condensed," not copy-pasted whole

`docs-kit/6-development/3-coding-standards.md` (and its siblings) carry the full document skeleton — Document Information metadata table, Assumptions/Constraints/Risks, Revision History, AI Generation Notes. None of that is useful loaded into every session's context; only the actionable rules themselves are. `.claude/rules/coding-standards.md` extracts just those — naming conventions, formatting rules, patterns to follow/avoid — in a compact form a session can act on immediately.

## New prompt: `3-document-generate/06-development/2-populate-claude-rules.md`

**Trigger:** runs once, right after `4-document-review/1-document-review.md` (scoped to `6-development` early wave) approves those six documents.

**Reads:** `docs-kit/6-development/1-development-environment.md` through `4-git-workflow.md` (the early-wave documents), specifically extracting the actionable-rule content from `2-folder-structure.md`, `3-coding-standards.md`, `4-git-workflow.md`.

**Writes:**
- `.claude/rules/coding-standards.md` — condensed from `docs-kit/6-development/3-coding-standards.md`.
- `.claude/rules/folder-structure.md` — condensed from `docs-kit/6-development/2-folder-structure.md`.
- `.claude/rules/git-workflow.md` — condensed from `docs-kit/6-development/4-git-workflow.md`.
- Root `CLAUDE.md` — updated to add (not replace wholesale) three `@import` lines for the files above, plus a short section pointing at `docs-kit/6-development/` for testing/deployment/debugging/containerization/CI-CD, by filename.

**Guardrail — confirm before overwriting:** `1-project-setup.md` already creates a skeletal root `CLAUDE.md` early in the project. This prompt must state what it's about to add to that existing file before writing (per `prompts/README.md`'s existing "Confirm before overwriting" rule) — never silently replace content the developer might have since hand-edited.

**Guardrail — stay in sync, don't duplicate drift:** if `3-coding-standards.md` (or the other two source docs) is later revised (e.g. via `1-discovery/7-change-request.md`), the corresponding `.claude/rules/*.md` file must be regenerated from the updated source, not left stale. This prompt's own "Resuming/regenerating" section should state how to re-run it scoped to just one rule file.

## Files touched (all under `full-doc/project-docs/`)

- **New:** `prompts/3-document-generate/06-development/2-populate-claude-rules.md`.
- `prompts/3-document-generate/06-development/development.md` — early-wave Next Step updated to point to the new prompt after its own review step.
- `prompts/README.md` — folder tree and phase table gain the new prompt's entry.
- `prompts/1-discovery/1-project-setup.md` — note that the skeletal `CLAUDE.md` it creates is not final; `2-populate-claude-rules.md` fills it in properly once early-wave docs are approved.
- `README.md` — new v9.8 changelog entry.

## Non-goals / explicitly deferred

- **Case B** (existing/legacy codebase, no `docs-kit/` yet — scanning real code/linter config for actual conventions, confirming with the developer, same pattern as the design-source auto-detect work) — separate future design pass.
- Hooks that mechanically enforce a rule (e.g. blocking a commit that violates git-workflow naming) — discussed and explicitly deferred; only documentation/`CLAUDE.md` generation is in scope here.
- Project-specific subagents/skills generated from `docs-kit/` (e.g. a "scaffold new module" skill matching `folder-structure.md`) — discussed and explicitly deferred.
- `on-demand-module-doc/project-docs/` — untouched, remains the JIT reference variant without this capability.

## Testing / verification

- `tools/validate-template.mjs` re-run against `full-doc/project-docs/prompts/` after the new prompt file and cross-references are added — confirms no broken "Next Step" chain and the new file carries a `**Prompt version:**` line.
- Manual read-through: `06-development/development.md`'s early-wave Next Step → `2-populate-claude-rules.md` → back to the normal upfront sequence, unbroken.
