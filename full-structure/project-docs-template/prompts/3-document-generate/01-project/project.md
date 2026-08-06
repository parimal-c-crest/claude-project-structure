# Generate: All Project Documents (1-project, batch)

**Prompt version:** 1.1

## Role
You are a technical writer / architect generating the full `1-project/` documentation set — all 4 documents — at professional quality, using the project's own templates as the required structure.

## Objective
Fill all 4 templates under `project-docs/docs-templates/1-project/templates/` and write the result to `project-docs/claude-docs/drafts/1-project/`, mirroring the template filenames exactly.

This is the only `1-project` prompt — it covers all 4 documents in one run. To regenerate a single document, re-run this prompt but scope step 2 (and the write in step 5) to just that one file instead of the full sweep.

## Resuming an interrupted run
If a previous run of this prompt stopped partway through (session ended, error, anything else), don't restart from document 1. Check `project-docs/claude-docs/drafts/1-project/` for which of the 4 documents already exist — resume from the next missing document, in the same numeric order, not from scratch.

## Prerequisites — stop and report if missing
- `project-docs/claude-docs/analysis/` (project-summary.md, module-list.md, business-rules-summary.md, workflow-summary.md) — from `1-discovery/5-project-analysis.md`. If missing, run that phase first, since `5-modules/modules.md` later depends on `module-list.md`.
- `project-docs/claude-docs/gap-analysis/gap-analysis-report.md` and `decisions-log.md` — from `1-discovery/6-gap-analysis.md`.

## Inputs
- The 4 templates in `project-docs/docs-templates/1-project/templates/` (`1-project-overview.md` → `4-tech-stack.md`) and `project-docs/docs-templates/1-project/README.md`.
- `project-docs/sot-docs/index.md` and the raw SoT documents it links to.
- `project-docs/claude-docs/analysis/project-summary.md`, `module-list.md`, `business-rules-summary.md`, `workflow-summary.md`.
- `project-docs/claude-docs/gap-analysis/gap-analysis-report.md` and `clarification-questions.md` for resolved decisions and logged assumptions.
- `project-docs/claude-docs/gap-analysis/decisions-log.md` — every locked cross-cutting decision; every document must reference these, never restate or diverge from them.
- `project-docs/claude-docs/plan/documentation-plan.md` for this category's declared dependencies.

## Instructions
1. Process the 4 documents **in numeric order** (`1-project-overview.md` → `4-tech-stack.md`) — later documents reference earlier ones in the same category, never the reverse.
2. For each document: read its template fully first — headings/structure are the contract, do not restructure. Read every already-approved dependency document it relies on from `project-docs/approved-docs/docs-kit/` before writing it — do not generate from memory of earlier phases alone.
3. Every requirement, rule, or design decision must trace back to a SoT source or a recorded decision/assumption — cite inline, e.g. `[Source: project-docs/sot-docs/raw/brd.md §6]` or `[Assumption: gap-analysis N2]`.
4. Where detail is insufficient, either draw a reasonable assumption clearly labeled `[Assumption: ...]`, or leave a `[NEEDS INPUT: ...]` marker — never invent unlabeled content.
5. Write each completed document directly to `project-docs/claude-docs/drafts/1-project/<template-filename>`, creating folders as needed. Never modify `project-docs/docs-templates/`.
6. Keep terminology consistent with `project-docs/sot-docs/index.md` and across all 4 documents in this batch.
7. After finishing all 4 documents, this category is complete.

## Output
- `project-docs/claude-docs/drafts/1-project/1-project-overview.md` … `4-tech-stack.md`

## Guardrails
- Don't skip a document; if something genuinely doesn't apply, still create the file with an explicit "Not Applicable — reason" note rather than omitting it.
- Never write into `project-docs/docs-templates/`.
- If a dependency document this batch relies on doesn't exist yet in `project-docs/approved-docs/docs-kit/`, stop and name it rather than guessing its content.

## Completion Checklist
- [ ] Prerequisites confirmed present before starting
- [ ] All 4 documents present, in numeric order
- [ ] Template read and structure followed exactly for each
- [ ] All content traceable to SoT, an approved document, or a labeled assumption
- [ ] Open `[NEEDS INPUT]` markers collected and listed for the user
- [ ] Terminology consistent across all 4 documents

## Next Step
`1-project/` drafts are complete. Run `project-docs/prompts/4-document-review/1-document-review.md` scoped to `1-project` next — nothing here is promoted into `approved-docs/docs-kit/` until it does, and `02-database/database.md`'s Prerequisites check for exactly that. Once `1-project/` is approved, run `project-docs/prompts/3-document-generate/02-database/database.md`. (`project-docs/claude-docs/analysis/` should already exist from `5-project-analysis.md`, run before generation started — if it's missing, run that phase first, since `05-modules/modules.md` later depends on its `module-list.md`.)
