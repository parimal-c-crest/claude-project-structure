# Generate: All Modules (5-modules, batch)

**Prompt version:** 1.3

## Role
You are a technical writer / architect generating one module's full documentation set — all 11 of its documents — at professional quality, using the project's own templates as the required structure. Run once per module, across every module in `module-list.md`.

## Objective
For one module at a time, in dependency order, fill all 11 templates under `project-docs/docs-templates/5-modules/templates/` and write the result to `project-docs/claude-docs/drafts/5-modules/<module-slug>/`, mirroring the template filenames exactly.

This is the only `5-modules` prompt — it covers all 11 documents for **one module per run**, matching every other batch file's cadence of one confirmed run → one review → one promotion, at module granularity rather than category granularity (`5-modules/` has no single "category" review the way `1-project/` does — each module is its own reviewable unit). Run this prompt once per module: draft the module's 11 documents, run `4-document-review/1-document-review.md` scoped to that module, then re-run this file for the next module in dependency order. To regenerate a single document for a single module, re-run this prompt but scope step 8 to just that module/file instead of a full module.

## Resuming an interrupted run
If a previous run of this prompt stopped partway through a module (session ended, error, anything else), don't restart that module from document 1. Check `project-docs/claude-docs/drafts/5-modules/<module-slug>/` for which of its 11 files already exist — resume from the next missing document, in numeric order. Also check `project-docs/approved-docs/docs-kit/5-modules/` for which modules are already fully approved — never regenerate a module that's already promoted; move on to the next one in dependency order instead.

## Prerequisites — stop and report if missing
- `project-docs/approved-docs/docs-kit/1-project/` fully generated and approved
- `project-docs/approved-docs/docs-kit/2-database/` fully generated and approved
- `project-docs/approved-docs/docs-kit/3-api/` fully generated and approved
- `project-docs/approved-docs/docs-kit/4-ui/` fully generated and approved
- `project-docs/claude-docs/analysis/module-list.md` — if missing, run `project-docs/prompts/1-discovery/5-project-analysis.md` first rather than guessing the module list.

Modules reference these global standards rather than repeating them — do not proceed with any module until all four exist. If one is missing or incomplete, name it and stop.

## Inputs
- `project-docs/claude-docs/analysis/module-list.md` — the full module list and slugs.
- The 11 templates in `project-docs/docs-templates/5-modules/templates/` (`1-module.md` → `11-testing.md`) and `project-docs/docs-templates/5-modules/README.md`.
- `project-docs/sot-docs/index.md` and the raw SoT documents it links to.
- `project-docs/claude-docs/analysis/project-summary.md`, `business-rules-summary.md`, `workflow-summary.md`.
- `project-docs/claude-docs/gap-analysis/gap-analysis-report.md` and `clarification-questions.md` for resolved decisions and logged assumptions.
- `project-docs/claude-docs/plan/documentation-plan.md` for module generation order and cross-module dependencies (e.g. a module that manages records depends on the module that defines who can own them).
- `project-docs/claude-docs/gap-analysis/decisions-log.md` — every locked cross-cutting decision; every module document must reference these, never restate or diverge from them.
- `project-docs/sot-docs/design/` — visual design references, if any exist. Check `project-docs/sot-docs/design/design-source.md` first.
- The approved global docs listed under Prerequisites.
- Already-completed modules under `project-docs/approved-docs/docs-kit/5-modules/` — later modules may reference earlier ones (e.g. a module reading another module's schema for a foreign key it depends on), never the reverse.

## Instructions
1. Read `module-list.md` and `documentation-plan.md` to get the module processing order (dependency order, not alphabetical — foundational modules like identity/access management typically come before modules that depend on them, which in turn come before modules that aggregate across several others, e.g. dashboards/reports).
2. Process **one module fully before starting the next**. Within a module, fill the 11 templates **in numeric order** (`1-module.md` → `11-testing.md`) — later documents in the set reference earlier ones in the same module, never the reverse.
3. For each document: read its template fully first — headings/structure are the contract, do not restructure. Read the global standards and any already-completed module docs it depends on.
4. Every requirement, rule, or design decision must trace back to a SoT source or a recorded decision/assumption — cite inline, e.g. `[Source: project-docs/sot-docs/raw/brd.md §6]` or `[Assumption: gap-analysis N2]`.
5. Where detail is insufficient, either draw a reasonable assumption clearly labeled `[Assumption: ...]`, or leave a `[NEEDS INPUT: ...]` marker — never invent unlabeled content.
6. Keep terminology, field names, and status/enum values consistent across all 11 docs within a module, and across modules that share entities (e.g. if two modules both reference the same record's status field, its allowed values must match in both) — and consistent with `decisions-log.md` specifically for anything cross-cutting (role scope, shared enums, ID conventions). Never let a module document quietly redecide something already locked there.
6a. **When writing `9-ui.md`, if a visual reference exists for this module's screens (per `design-source.md`), walk every one of them literally first.** For each screen, record the concrete, observable facts — exact fields/buttons/labels/copy, layout regions, component patterns reused from `4-ui/3-design-system.md` — and any state only reachable by interacting (a drawer, a modal, a feed that only renders after a click), as a specific checklist, not generic prose. This is what implementation will build directly from — vague, general-purpose descriptions here are exactly how the built UI ends up drifting from the reference without anyone noticing until a later screenshot comparison catches it.
7. Write each completed document directly to `project-docs/claude-docs/drafts/5-modules/<module-slug>/<template-filename>`, creating folders as needed. Never modify `project-docs/docs-templates/`.
8. Stop once this module's 11 documents are all drafted — do not continue on to the next module in the same run. Go to review (see Next Step) before starting another module.

## Output
- `project-docs/claude-docs/drafts/5-modules/<slug>/1-module.md` … `11-testing.md`, for the one module processed this run.

## Guardrails
- Don't parallelize modules against each other if one depends on another (per `documentation-plan.md`) — finish the dependency first.
- Don't start a second module in the same run — one module's 11 documents, then stop for review.
- Don't skip a document within a module; if something genuinely doesn't apply, still create the file with an explicit "Not Applicable — reason" note rather than omitting it.
- Never write into `project-docs/docs-templates/`.
- If a global standard or an inter-module dependency is missing, stop and name it rather than guessing.

## Completion Checklist
- [ ] All four global categories (`1-project`, `2-database`, `3-api`, `4-ui`) confirmed approved before starting
- [ ] This module's 11 documents present, in numeric order
- [ ] All content traceable to SoT, an approved document, or a labeled assumption
- [ ] Open `[NEEDS INPUT]` markers collected and listed for the user
- [ ] Terminology/status values consistent with modules already approved that share entities

## Next Step
This module's drafts are complete. Run `project-docs/prompts/4-document-review/1-document-review.md` scoped to `5-modules/<module-slug>` next — nothing here is promoted into `approved-docs/docs-kit/` until it does. Once this module is approved, re-run this same file for the next module in dependency order per `module-list.md`/`documentation-plan.md`. Once every module is approved, run `project-docs/prompts/3-document-generate/06-development/development.md` for its late wave (`5-implementation-workflow.md` onward — the wave that references the finished modules).
