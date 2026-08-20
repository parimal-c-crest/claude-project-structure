# Upfront module documentation flow — design spec

**Date:** 2026-08-20
**Scope:** `full-doc/project-docs/` only. `on-demand-module-doc/project-docs/` stays unchanged as the just-in-time (JIT) reference variant. The two folders are currently byte-identical (both JIT); this spec makes `full-doc/` diverge.

## Problem

`full-doc/project-docs/` currently implements the same JIT flow as `on-demand-module-doc/`: `5-modules/` docs and `6-development/` late-wave docs are generated per module, triggered by `7-sprint-planning/1-sprint-planning.md` step 2a, only when that module's epic becomes a sprint candidate. Documentation is spread across the whole build timeline.

The user wants a second variant where **all module documentation (5-modules + 6-development late wave + cross-cutting) is generated for every module before any sprint planning or implementation starts.**

Milestone/implementation-plan staging (the UI-all-modules-then-backend-one-at-a-time split) is explicitly out of scope for this change — deferred to a future discussion. The task-list itself should be derived once, in full, after all docs are approved (confirmed by user), but the milestone *shape* that organizes those tasks is unchanged for now.

## Chosen approach

Keep `5-modules/modules.md` and `6-development/development.md`'s late wave exactly as they work today — one module per run, review checkpoint, resumable, late-wave docs incrementally updated per module. Only change **when** they're triggered: move the trigger out of `7-sprint-planning` step 2a into a new upfront loop that runs immediately after the four global categories (`1-project`, `2-database`, `3-api`, `4-ui`) and `6-development` early wave are approved.

Rejected alternative: rewriting `modules.md` to loop over every module inside one run. Rejected because it discards the proven one-module-per-session/resume/review design for no real benefit — the same effect (all modules documented before sprints start) is achieved by relocating the trigger instead.

## New upfront sequence

1. `1-project` → `2-database` → `3-api` → `4-ui` → `6-development` early wave (unchanged, parallel-safe once `1-project` is approved).
2. `5-update-sot/1-update-sot.md` once.
3. **New module loop** — for each module in `claude-docs/analysis/module-list.md`, in dependency order per `claude-docs/plan/dependencies.md`:
   a. `3-document-generate/05-modules/0-field-extraction.md` for this module, if not already done.
   b. `3-document-generate/05-modules/modules.md`, scoped to this module → all 11 docs.
   c. `4-document-review/1-document-review.md`, scoped to this module → promote to `approved-docs/docs-kit/5-modules/<slug>/`.
   d. `3-document-generate/06-development/development.md`, late wave, scoped to this module (creates the 4 late-wave docs on the first module, updates them incrementally on each subsequent module — unchanged behavior from today).
   e. `4-document-review/1-document-review.md`, scoped to that late-wave slice → promote.
   f. `5-update-sot/1-update-sot.md`, folding this module in.
   g. Move to the next module in the list. Repeat until every module is done.
4. `07-cross-cutting/cross-cutting.md` → review → `4-document-review/2-documentation-review.md` (final full-tree sweep) → `5-update-sot`. Runs here now — no longer deferred to "whenever the last module's JIT cycle finishes," since there is no more JIT.
5. `6-implementation-plan/1-implementation-plan.md` once, deriving the full task list for every module's epics (milestone shape unchanged — deferred discussion).
6. `7-sprint-planning/1-sprint-planning.md` — **step 2a (JIT gate) removed.** Sprint planning becomes a pure task-picker; every epic it considers already has approved docs and a real task list.

## Files touched (all under `full-doc/project-docs/`)

- `prompts/README.md` — replace the "Upfront documentation generation loop" / "Sprint loop, including JIT gate" / "Cross-cutting, deferred" bullets (lines ~219-221) and the flow diagram/table (lines ~21, 27, 43, 45, 63-64) to describe the new sequence. Remove language describing `5-modules`/`6-development` late wave as JIT-triggered.
- `prompts/3-document-generate/05-modules/modules.md` — rewrite the "Trigger" section: no longer triggered by `7-sprint-planning` step 2a; instead runs once per module inside the upfront module loop (step 3 above), right after `4-ui` and `6-development` early wave are approved. Keep everything else (one module per run, 11 docs, resume behavior, guardrails) unchanged. Update "Next Step" to point at the next module in the loop, or at `07-cross-cutting` if this was the last module.
- `prompts/3-document-generate/06-development/development.md` — update late-wave Trigger/Prerequisites/Next Step: fires inside the module loop (step 3d) right after that module's `5-modules/modules.md` is approved, not via sprint-planning. Its "last module → cross-cutting" check moves from "Next Step" step-2a-referencing text to plain sequencing within the upfront loop.
- `prompts/3-document-generate/07-cross-cutting/cross-cutting.md` — Prerequisites/Trigger text: runs once, right after the module loop completes (no longer "deferred," no longer checking sprint-planning state).
- `prompts/7-sprint-planning/1-sprint-planning.md` — delete step 2a in full (the JIT gate paragraph, including the `<Module> — UI Design` immediate-hand-off note), and its corresponding line in the Completion Checklist ("Just-in-time module documentation gate (step 2a) run for any candidate epic..."). Sprint planning's remaining steps (1, 2, 3-9) are unchanged.
- `prompts/3-document-generate/04-ui/ui.md` — update its "Next Step" line, which currently says `5-modules/modules.md` does not start here under the JIT model; change to say the module loop starts here (after early-wave `6-development` also completes).

## Non-goals / explicitly deferred

- Milestone 2/3+ staging (UI-all-modules-first vs backend-one-module-at-a-time) — unchanged in this pass.
- `on-demand-module-doc/project-docs/` — untouched, remains the JIT reference variant.
- `docs-templates/README.md` staleness (found during initial folder review) — not addressed here.

## Testing / verification

- `tools/validate-template.mjs` (already category-aware for `01`/`1` numbering) should be re-run against `full-doc/project-docs/prompts/` after edits to confirm no broken cross-references were introduced.
- Manual read-through: every "Next Step" line in the touched files should form one unbroken chain from `4-ui` through the module loop to `07-cross-cutting` to `6-implementation-plan` to `7-sprint-planning`, with no remaining reference to step 2a or JIT triggering.
