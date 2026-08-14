# project-docs

Living documentation for this project — problem/scope, database, API, UI, dev standards, tasks, and decisions, kept in `docs-output/`. Generated from `project-docs-template-lite`, a lightweight documentation kit for small/mid projects — solo or pair developer, roughly up to 8-10 modules, a build measured in days/weeks rather than months. Where `project-docs-template/` (the full kit) is built for large, multi-milestone, multi-developer projects with 7 categories/49 documents/12 phases, this kit covers the same essential ground in 7 flat files and no separate prompt/pipeline machinery.

**Outgrown this kit?** Migrate to `project-docs-template/` once any of these is true: more than ~8-10 modules, more than 2 developers working in parallel, or the project is running past a couple of months. Past that point this kit's flat files and single-pass review stop being enough — the big kit's draft/review/promote gate and per-module isolation start earning their cost.

## How to use

1. To start a new project with this kit: copy `project-docs-template-lite/project-docs/` into the new project's root, keeping the name `project-docs/`.
2. Follow the 5-step workflow below, in order.

## The 5-step workflow

1. **Setup** — Ask the developer the intake questions each skeleton file's headers imply (problem/goal, tech stack, entities, screens, coding standards). Create the project's root `CLAUDE.md` from the answers so future sessions start oriented.
2. **Generate** — Fill all 7 files in `docs-output/` from those answers plus any existing code, in one pass. No batching, no per-category confirmation — one pass, one sitting.
3. **Review** — Read each filled file once, critically. Fix anything wrong, vague, or missing before treating it as done. There is no separate draft→review→promote pipeline at this scale — this single read is the review.
4. **Implement** — Pick the next `Todo` task from `docs-output/tasks.md`, build it, test it, mark it `Done`.
5. **Sync** — After a task lands, check whether the code diverged from `docs-output/database.md`/`api.md`/`ui.md`. Patch the doc if so. A task is not actually `Done` until this check happens — not just "tests pass."

## The two non-negotiable rules

- **Ask, don't assume.** When something in a document is unclear or undecided, ask the developer. Only use `[Assumption: ...]` labeling when they've explicitly said "use your judgment" for that point.
- **Review before done.** Never generate a document and immediately treat it as final. Read it once, critically, before calling it done — this is what step 3 above exists to enforce.

These two rules carry most of the quality protection that the big kit's dedicated prompt files and review pipeline provide — at this scale, stating them plainly is enough; a bigger enforcement mechanism isn't earning its cost yet.

## What's included

- `docs-templates/` — the 7 read-only skeleton files. Never edit these directly; they're the blueprint the `docs-output/` files are filled from.
- `docs-output/` — an empty scaffold matching the skeleton, filled in per real project once copied.

## Why no `prompts/` folder

The big kit's prompt files exist to force identical behavior across many files, weeks, and developers — batching rules, wave gates, guardrails, version tracking. At 7 static files, filled in one sitting, that discipline mechanism costs more than it returns; the two rules above plus the skeleton headers do the job instead. If a step here ever grows real complexity, split it into its own file then — not preemptively.

## Why no module-wise files

Modules are sections inside the shared documents (a table row in `database.md`, an endpoint group in `api.md`, a screen group in `ui.md`), not their own file set. This keeps total file count flat regardless of module count — content quality doesn't suffer, since every module still gets its schema/endpoints/screens documented, just consolidated rather than partitioned.
