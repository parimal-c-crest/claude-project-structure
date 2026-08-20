# Update Source of Truth

**Prompt version:** 1.2

## Role
You are the documentation owner responsible for keeping a single, current Source of Truth.

## Objective
Promote newly approved documentation (now living under `project-docs/approved-docs/docs-kit/`) into the official Source of Truth index, so it — not stale raw notes — becomes the authoritative reference for every later phase.

## Inputs
- All documents newly approved into `project-docs/approved-docs/docs-kit/` this cycle (per `project-docs/claude-docs/gap-analysis/review-log.md`).
- `project-docs/sot-docs/index.md`.

## Instructions
1. For each newly approved document under `docs-kit/`, add or update its entry in `project-docs/sot-docs/index.md`, pointing to its `docs-kit/<category>/...` path and marking it as the current authoritative version for its topic.
2. Where an approved document supersedes older raw material in `project-docs/sot-docs/raw/` (e.g., an approved `1-project-overview.md` supersedes an old notes file covering the same ground), do not delete the old file — move it to `project-docs/sot-docs/archive/` and note the supersession in the index.
3. Record a change history entry in `project-docs/sot-docs/changelog.md`: what changed, which documents, date, and why (link to the review log entry).
4. Confirm there are no unresolved conflicts between the newly promoted documents and the existing SoT; if there are, route them back to `project-docs/prompts/1-discovery/6-gap-analysis.md` rather than resolving them silently.
5. Update the Glossary (if one exists, likely `docs-kit/1-project/`) with any new terms introduced.

## Output
- Updated `project-docs/sot-docs/index.md`.
- `project-docs/sot-docs/archive/` populated with superseded materials.
- Updated `project-docs/sot-docs/changelog.md`.

## Guardrails
- Never delete superseded material — archive it.
- The SoT index should point into `project-docs/approved-docs/docs-kit/`, not duplicate its content.
- The SoT must always reflect the latest approved state; don't leave it partially updated.
- Original raw material in `project-docs/sot-docs/raw/` is still never edited in place — superseded files move to `archive/` whole, unmodified.

## Completion Checklist
- [ ] `project-docs/sot-docs/index.md` updated with new authoritative documents (pointing into `docs-kit/`)
- [ ] Superseded materials archived, not deleted
- [ ] `project-docs/sot-docs/changelog.md` entry added
- [ ] No unresolved conflicts remain

## Next Step
This prompt runs more than once, once per cycle in the upfront flow — its Next Step depends on which cycle just finished:
- **Initial run** (right after the upfront categories — `1-project`, `2-database`, `3-api`, `4-ui`, `6-development` early wave — are approved): run `project-docs/prompts/3-document-generate/05-modules/modules.md` next, scoped to the first module in `module-list.md` — the upfront module loop starts.
- **Per-module run** (after a module's `05-modules/` and `06-development/` late-wave documents are approved, inside the module loop): check `module-list.md` — if any module remains undocumented, re-run `3-document-generate/05-modules/modules.md` scoped to the next one in dependency order; once every module is done, run `3-document-generate/07-cross-cutting/cross-cutting.md` next.
- **Final run** (triggered by `project-docs/prompts/3-document-generate/07-cross-cutting/cross-cutting.md`'s Next Step, once `07-cross-cutting/` and `4-document-review/2-documentation-review.md` are both done): run `project-docs/prompts/6-implementation-plan/1-implementation-plan.md` next — every module's docs already exist, so it derives the full Milestone/Epic/Task set for the whole project in one pass.
