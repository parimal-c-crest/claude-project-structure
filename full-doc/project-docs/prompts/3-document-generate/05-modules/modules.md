# Generate: All Modules (5-modules, batch)

**Prompt version:** 1.8

## Role
You are a technical writer / architect generating one module's full documentation set — all 11 of its documents — at professional quality, using the project's own templates as the required structure.

## Trigger — upfront module loop, before any sprint or implementation
Under this variant's flow, this prompt runs for **every** module, upfront — once `01-project`, `02-database`, `03-api`, `04-ui`, and `06-development`'s early wave are all approved, and `5-update-sot/1-update-sot.md` has folded them into the SoT. It's the first step of the **module loop**: run once per module, in dependency order (`claude-docs/plan/dependencies.md`), looping through every module in `claude-docs/analysis/module-list.md` until all are documented — before `7-sprint-planning/` or any implementation starts. The first time a module reaches this loop, its run starts with step 0 below (field & rule extraction) before any of the 11 documents are drafted.

## Objective
For one module, fill all 11 templates under `project-docs/docs-templates/5-modules/templates/` and write the result to `project-docs/claude-docs/drafts/5-modules/<module-slug>/`, mirroring the template filenames exactly.

This is the only `5-modules` prompt — it covers all 11 documents for **one module per run**, matching every other batch file's cadence of one confirmed run → one review → one promotion, at module granularity rather than category granularity (`5-modules/` has no single "category" review the way `1-project/` does — each module is its own reviewable unit). Run this prompt once per module, in the upfront module loop (see Trigger above): draft the module's 11 documents, run `4-document-review/1-document-review.md` scoped to that module, then continue the loop's remaining steps for this module (late-wave `6-development/` docs, its own review, an SoT fold-in) before moving to the next module in `module-list.md`. To regenerate a single document for a single module, re-run this prompt but scope step 8 to just that module/file instead of a full module.

## Resuming an interrupted run
If a previous run of this prompt stopped partway through a module (session ended, error, anything else), don't restart that module from document 1. Check `project-docs/claude-docs/drafts/5-modules/<module-slug>/` for which of its 11 files already exist — resume from the next missing document, in numeric order.

## Prerequisites — stop and report if missing
- `project-docs/approved-docs/docs-kit/1-project/` fully generated and approved
- `project-docs/approved-docs/docs-kit/2-database/` fully generated and approved
- `project-docs/approved-docs/docs-kit/3-api/` fully generated and approved
- `project-docs/approved-docs/docs-kit/4-ui/` fully generated and approved
- `project-docs/claude-docs/analysis/module-list.md` — if missing, run `project-docs/prompts/1-discovery/5-project-analysis.md` first rather than guessing the module list.
- **This module's own dependencies, if any are recorded in `project-docs/claude-docs/plan/dependencies.md`** (e.g. a module that manages records depends on the module that defines who can own them) **are already approved under `docs-kit/5-modules/`.** The module loop processes `module-list.md` in dependency order already, so this is normally satisfied automatically — but if this module was reached out of order, stop and name the missing dependency rather than guessing its content.
- **`project-docs/claude-docs/analysis/module-field-extraction/<module-slug>/`** — this module's
  exhaustive field-by-field and rule-by-rule extraction (`entities-and-fields.md`,
  `business-rules.md`, and `workflow.md` if applicable) must exist, with no unresolved Blocking
  items in its `open-questions.md`. If missing or incomplete, run `0-field-extraction.md` in this
  same folder first — do not draft `3-business-rules.md`, `4-schema.md`, `5-data-dictionary.md`,
  or `6-validation.md` straight from the SoT/summary analysis.

Modules reference these global standards rather than repeating them — do not proceed with any module until all four exist. If one is missing or incomplete, name it and stop.

## Inputs
- `project-docs/claude-docs/analysis/module-list.md` — the full module list and slugs.
- `project-docs/claude-docs/analysis/module-field-extraction/<module-slug>/` — this module's
  exhaustive field and rule catalog. Required primary source for `3-business-rules.md`,
  `4-schema.md`, `5-data-dictionary.md`, and `6-validation.md` — supersedes
  `business-rules-summary.md`/the raw SoT for those four documents specifically; the other seven
  documents still read the broader inputs listed below as usual.
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
0. Before anything else: check `project-docs/claude-docs/analysis/module-field-extraction/
   <module-slug>/` for this module. If it doesn't exist, or its `open-questions.md` has an
   unresolved Blocking item, stop this run and go execute `0-field-extraction.md` (this same
   folder) for this module first — do not draft any of this module's 11 documents from the SoT/
   summary analysis directly. Resume this prompt at step 1 once extraction is complete.
1. This run is scoped to whichever single module the module loop is currently on — the next module in `module-list.md`, in dependency order, that doesn't yet have approved docs under `docs-kit/5-modules/`. Read `module-list.md` for that module's slug and `dependencies.md` for anything it depends on (see Prerequisites above).
2. Fill the 11 templates **in numeric order** (`1-module.md` → `11-testing.md`) for this module only — later documents in the set reference earlier ones in the same module, never the reverse.
3. For each document: read its template fully first — headings/structure are the contract, do not restructure. Read the global standards and any already-completed module docs it depends on.
4. Every requirement, rule, or design decision must trace back to a SoT source, a recorded decision/assumption, or (for `3-business-rules.md`/`4-schema.md`/`5-data-dictionary.md`/`6-validation.md` specifically) a rule ID or field from this module's field-extraction documents — cite inline, e.g. `[Source: project-docs/sot-docs/raw/brd.md §6]`, `[Assumption: gap-analysis N2]`, or `[Source: module-field-extraction/<slug>/business-rules.md, <MODULE>-RULE-014]`.
5. **Never silently assume.** Where detail is insufficient, note it as an open question while drafting — don't write a guessed value into the document yet. Once this document is otherwise fully drafted, stop and ask the user every open question for it together, in one plain-language round (not as separate interruptions per question). Only write the final content after the user answers: use their real answer if given; if they explicitly say to use your own judgment, write `[Assumption: ...]` — a deferred call the user actually approved, not a silent guess. Reserve `[NEEDS INPUT: ...]` for something genuinely blocking even after asking (the user doesn't know either, needs to check something first) — not a substitute for asking in the first place.
6. Keep terminology, field names, and status/enum values consistent across all 11 docs within a module, and across modules that share entities (e.g. if two modules both reference the same record's status field, its allowed values must match in both) — and consistent with `decisions-log.md` specifically for anything cross-cutting (role scope, shared enums, ID conventions). Never let a module document quietly redecide something already locked there.
6a. **When writing `9-ui.md`, if a visual reference exists for this module's screens (per `design-source.md`), walk every one of them literally first.** For each screen, record the concrete, observable facts — exact fields/buttons/labels/copy, layout regions, component patterns reused from `4-ui/3-design-system.md` — and any state only reachable by interacting (a drawer, a modal, a feed that only renders after a click), as a specific checklist, not generic prose. This is what implementation will build directly from — vague, general-purpose descriptions here are exactly how the built UI ends up drifting from the reference without anyone noticing until a later screenshot comparison catches it.
7. Write each completed document directly to `project-docs/claude-docs/drafts/5-modules/<module-slug>/<template-filename>`, creating folders as needed. Never modify `project-docs/docs-templates/`.
8. Stop once this module's 11 documents are all drafted — do not continue on to the next module in the same run. Go to review (see Next Step) before starting another module.

## Output
- `project-docs/claude-docs/drafts/5-modules/<slug>/1-module.md` … `11-testing.md`, for the one module processed this run.

## Guardrails
- Never start drafting the next document in this module's set while the current one has an open, unanswered question — resolve it first (a real user answer, or an explicit "use your judgment" recorded as `[Assumption: ...]`), per step 5's Never-silently-assume rule.
- Don't parallelize modules against each other if one depends on another (per `documentation-plan.md`) — finish the dependency first.
- Don't start a second module in the same run — one module's 11 documents, then stop for review.
- Don't skip a document within a module; if something genuinely doesn't apply, still create the file with an explicit "Not Applicable — reason" note rather than omitting it.
- Never write into `project-docs/docs-templates/`.
- If a global standard or an inter-module dependency is missing, stop and name it rather than guessing.

## Completion Checklist
- [ ] This module's field-extraction documents exist, with no unresolved Blocking open questions
- [ ] All four global categories (`1-project`, `2-database`, `3-api`, `4-ui`) confirmed approved before starting
- [ ] This module's 11 documents present, in numeric order
- [ ] All content traceable to SoT, an approved document, or a labeled assumption
- [ ] Open `[NEEDS INPUT]` markers collected and listed for the user
- [ ] No `[Assumption: ...]` was written without first asking the user and getting an explicit "use your judgment" response
- [ ] Terminology/status values consistent with modules already approved that share entities

## Next Step
This module's drafts are complete. Run `project-docs/prompts/4-document-review/1-document-review.md` scoped to `5-modules/<module-slug>` next — nothing here is promoted into `approved-docs/docs-kit/` until it does. Once approved, the module loop continues for this same module: `3-document-generate/06-development/development.md` late wave, scoped to this module, then its own review, then `5-update-sot/1-update-sot.md` to fold this module in. Once that's done, check `module-list.md`: if any module remains undocumented, re-run this prompt scoped to the next one in dependency order. Once every module is done, run `3-document-generate/07-cross-cutting/cross-cutting.md` next — the module loop is complete.
