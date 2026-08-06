# Generate: All Development Documents (6-development, batch)

**Prompt version:** 1.1

## Role
You are a technical writer / architect generating the full `6-development/` documentation set — all 10 documents — at professional quality, using the project's own templates as the required structure.

## Objective
Fill all 10 templates under `project-docs/docs-templates/6-development/templates/` and write the result to `project-docs/claude-docs/drafts/6-development/`, mirroring the template filenames exactly.

This is the only `6-development` prompt — it covers all 10 documents, run as **two waves** (see below), not one flat pass. To regenerate a single document, re-run this prompt but scope the relevant instruction below (and the write) to just that one file instead of the full sweep.

## Two waves — this category has a real dependency gate, not just a size split
Unlike the other batched categories, `6-development/`'s 10 documents split into two groups with a hard dependency between them:
- **Early wave** (`1-development-environment.md`, `2-folder-structure.md`, `3-coding-standards.md`, `4-git-workflow.md`, `8-containerization.md`, `9-ci-cd.md`) — no dependency on modules, can run as soon as `1-project/` is approved, in parallel with `02-database/`, `03-api/`, `04-ui/`.
- **Late wave** (`5-implementation-workflow.md`, `6-testing-strategy.md`, `7-deployment-strategy.md`, `10-debugging-guide.md`) — references the finished module set, and must wait until every module in `05-modules/` is complete.

Run this prompt once for the early wave (6 documents, one confirmation), then again for the late wave (4 documents, one confirmation) once `05-modules/modules.md` has finished every module. Each wave is still one batch, one confirmation — the split is structural, not a reintroduction of per-document stops.

## Resuming an interrupted run
If a previous run of either wave stopped partway through, don't restart that wave from its first document. Check `project-docs/claude-docs/drafts/6-development/` for which documents already exist — resume from the next missing document in that wave's set, in numeric order, not from scratch.

## Prerequisites — stop and report if missing
- Early wave: `project-docs/approved-docs/docs-kit/1-project/` fully generated and approved.
- Late wave: every module in `project-docs/claude-docs/analysis/module-list.md` complete under `project-docs/claude-docs/drafts/5-modules/` (or approved under `project-docs/approved-docs/docs-kit/5-modules/`) — if any module is incomplete, name it and wait rather than generating the late wave against a partial module set.

## Inputs
- The 10 templates in `project-docs/docs-templates/6-development/templates/` (`1-development-environment.md` → `10-debugging-guide.md`) and `project-docs/docs-templates/6-development/README.md`.
- `project-docs/sot-docs/index.md` and the raw SoT documents it links to.
- `project-docs/claude-docs/analysis/project-summary.md`, `module-list.md`, `business-rules-summary.md`, `workflow-summary.md`.
- `project-docs/claude-docs/gap-analysis/gap-analysis-report.md`, `clarification-questions.md`, `decisions-log.md`.
- `project-docs/claude-docs/plan/documentation-plan.md` for this category's declared dependencies.
- Late wave only: the completed `5-modules/` set, for `5-implementation-workflow.md` and `6-testing-strategy.md` to reference real module structure.

## Instructions
1. Within each wave, process its documents in numeric order — later documents in the same wave reference earlier ones in that wave, never the reverse.
2. For each document: read its template fully first — headings/structure are the contract, do not restructure. Read the earlier documents this batch (or an earlier wave of this same batch) has already drafted in `project-docs/claude-docs/drafts/6-development/` that it depends on, plus any approved dependency from another category in `project-docs/approved-docs/docs-kit/`, before writing it.
3. Every requirement, rule, or design decision must trace back to a SoT source or a recorded decision/assumption — cite inline, e.g. `[Source: project-docs/sot-docs/raw/brd.md §6]` or `[Assumption: gap-analysis N2]`.
4. Where detail is insufficient, either draw a reasonable assumption clearly labeled `[Assumption: ...]`, or leave a `[NEEDS INPUT: ...]` marker — never invent unlabeled content.
5. Keep terminology and standards consistent across all 10 documents, both within a wave and across the two waves.
6. Write each completed document directly to `project-docs/claude-docs/drafts/6-development/<template-filename>`, creating folders as needed. Never modify `project-docs/docs-templates/`.

## Output
- Early wave: `project-docs/claude-docs/drafts/6-development/1-development-environment.md`, `2-folder-structure.md`, `3-coding-standards.md`, `4-git-workflow.md`, `8-containerization.md`, `9-ci-cd.md`.
- Late wave: `project-docs/claude-docs/drafts/6-development/5-implementation-workflow.md`, `6-testing-strategy.md`, `7-deployment-strategy.md`, `10-debugging-guide.md`.

## Guardrails
- Don't skip a document; if something genuinely doesn't apply, still create the file with an explicit "Not Applicable — reason" note rather than omitting it.
- Never write into `project-docs/docs-templates/`.
- Never generate the late wave before every module in `05-modules/` is complete — stop and name what's missing instead.
- If a dependency document outside this batch (e.g. `1-project/`) doesn't exist yet in `project-docs/approved-docs/docs-kit/`, stop and name it rather than guessing its content.

## Completion Checklist
- [ ] Correct wave's prerequisites confirmed before starting that wave
- [ ] All 6 early-wave documents present, in numeric order (first run)
- [ ] All 4 late-wave documents present, in numeric order (second run, after `05-modules/` complete)
- [ ] All content traceable to SoT, an approved document, or a labeled assumption
- [ ] Open `[NEEDS INPUT]` markers collected and listed for the user
- [ ] Terminology and standards consistent across both waves

## Next Step
After the early wave: run `project-docs/prompts/4-document-review/1-document-review.md` scoped to `6-development` (early-wave documents) — nothing here is promoted into `approved-docs/docs-kit/` until it does. `4-ui/` and `3-api/` continue in parallel; `05-modules/modules.md` can also begin once `02-database/`, `03-api/`, `04-ui/` are approved.
After the late wave: run `project-docs/prompts/4-document-review/1-document-review.md` scoped to `6-development` (late-wave documents) next. Once all ten `6-development/` documents are approved, run `project-docs/prompts/3-document-generate/07-cross-cutting/cross-cutting.md` — the cross-cutting category runs last because its two documents (NFR, threat model) cross-check everything decided in every other category.
