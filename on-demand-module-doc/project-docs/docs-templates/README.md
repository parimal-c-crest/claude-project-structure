# Project Documentation Framework — Template Blueprints

> **Purpose**
>
> `project-docs/docs-templates/` is the **read-only, standardized blueprint library** this project's documentation is generated from — before any development begins. It defines *what* needs to be planned, designed, and agreed upon — across business requirements, architecture, database, API, UI, individual feature modules, and development practices — so that any AI coding assistant (or human developer) can pick up the project with full context and build it consistently, without guessing or re-asking basic questions.
>
> This README explains the blueprints themselves. It is **not** the entry point for actually generating documentation — that's `project-docs/prompts/README.md`, which drives the generate → review → promote flow that reads these templates and writes real output elsewhere. Read this file to understand what each template covers and how they're structured; read `prompts/README.md` to actually run the workflow.

---

## What This Is

This is a **template-driven, AI-first documentation system**. Every template in this folder:

- Follows the **same standard structure** (see "Document Template Pattern" below).
- Is filled in **per project**, by the prompts under `project-docs/prompts/3-document-generate/`, never edited here directly.
- Is written so an **AI tool can read it and generate correct, consistent output** — code, schemas, APIs, UI, tests — without needing external clarification.
- Cross-references related documents so context can be traced end-to-end (business need → architecture → database → API → UI → module → implementation → testing).

These files here are **blank templates, never written to directly** — generation writes to `project-docs/claude-docs/drafts/`, and only promotes to `project-docs/approved-docs/docs-kit/` after review. This folder is the fixed skeleton; `docs-kit/` (not this folder) becomes the project's actual filled-in source of truth once generation runs.

---

## Folder Structure

```
project-docs/docs-templates/
│
├── 1-project/         Business context, requirements, features, tech stack
├── 2-database/        Database design, ERD, migrations, standards
├── 3-api/              API design, auth, standards, versioning, OpenAPI/Postman
├── 4-ui/                Navigation, UX flows, design system, component/accessibility standards
├── 5-modules/         Per-feature documentation (one folder per business module) + templates/
├── 6-development/    Dev environment, workflow, CI/CD, testing, deployment
├── 7-cross-cutting/  Non-functional requirements and threat model — concerns spanning every module
└── README.md           This file
```

### Read/Build Order

The folders are numbered because they represent the **order in which decisions should be made** — later folders depend on earlier ones:

```
1-project            →  What are we building and why?
        ↓
2-database           →  What data does it need to store?
        ↓
3-api                →  How is that data exposed and consumed?
        ↓
4-ui                 →  How do users interact with it?
        ↓
5-modules             →  Feature-by-feature detailed specs (uses 1-4 as shared standards)
        ↓
6-development         →  How is it built, tested, and shipped?
        ↓
7-cross-cutting       →  What quality/security bar must all of the above meet?
```

An AI tool should **not** jump straight to writing code or designing a module without first understanding `1-project` (especially requirements, feature breakdown, and tech stack) and the relevant global standards in `2-database`, `3-api`, and `4-ui`.

**Generation order in practice** is driven by `project-docs/prompts/README.md`, not this file — in this `on-demand-module-doc` variant, `5-modules/` and `6-development/`'s late wave are **just-in-time**: deferred until a module's epic is actually about to enter a sprint (`7-sprint-planning/1-sprint-planning.md` step 2a), not generated upfront with the rest. `7-cross-cutting/` then waits until the last module's JIT cycle completes. See `prompts/README.md`'s "Just-in-time module documentation" section for the exact current sequence — it's the authoritative source, this README only explains what each template covers.

---

## Folder-by-Folder Purpose

| Folder | Purpose |
|---|---|
| **1-project/** | Project overview, requirements, feature breakdown, and the official tech stack. The business "why" and "what" of the whole project. |
| **2-database/** | Database design, ERD, standards (naming, indexing, constraints), and migration strategy — applies globally across all modules. |
| **3-api/** | Global API architecture and standards: design principles, auth/authorization, request/response/error/versioning conventions, plus `openapi.yaml` and a Postman collection. |
| **4-ui/** | Global UI/UX architecture: navigation, user flows, design system, component/form standards, responsive design, and accessibility. |
| **5-modules/** | One subfolder per business feature (e.g. `authentication/`, `users/`, `products/`, `orders/`), generated by `prompts/3-document-generate/05-modules/modules.md`. Each module gets a full set of 11 documents describing that one feature end-to-end. Has its own `README.md` explaining the module framework in detail, and a `templates/` folder mirrored by the generation prompt. |
| **6-development/** | How the project is actually built day-to-day: local dev environment, folder structure, coding standards, git workflow, implementation workflow, testing, deployment, containerization, CI/CD, debugging. Split into an early wave (project-wide, no module dependency) and a late wave (references real module structure). |
| **7-cross-cutting/** | Concerns that apply across every module rather than one: measurable non-functional requirements (performance, availability, scalability, security, operability) and the system-wide threat model. Generated last, after everything else, since it cross-checks decisions made everywhere. |

---

## Document Template Pattern

Every document (outside of `5-modules/README.md` and `5-modules/templates/`) follows the **same skeleton**, so an AI tool can parse any file in this repo the same way:

1. **Title + Purpose** — a blockquote explaining what this specific document is for.
2. **Document Information** — a metadata table (project name, version, status, author, dates).
3. **Numbered sections** (`# 1. ...`, `# 2. ...` etc.) — the actual content, each with instructional text or examples showing what should go there (these are prompts for whoever/whatever fills the doc in, not final content).
4. **Assumptions / Constraints / Risks** — explicitly captured, not left implicit.
5. **Related Documents** — links to other docs this one depends on or feeds into, for traceability.
6. **Revision History** and **Approval** — version control and sign-off tracking.
7. **AI Generation Notes** — explicit instructions for an AI tool on how to fill in *this specific document*: what to focus on, what to avoid, and which other documents to stay consistent with.

**The "AI Generation Notes" section at the bottom of each file is the most important part for an AI tool** — always read it before generating or editing that document.

---

## The Module System (`5-modules/`)

Individual business features (User, Product, Order, Invoice, etc.) are **not** documented inside the global folders — they get their own self-contained folder under `5-modules/` (in generated output, under `claude-docs/drafts/5-modules/<slug>/` then `approved-docs/docs-kit/5-modules/<slug>/`), each with 11 standard documents (module overview, functional spec, business rules, schema, data dictionary, validation, permissions, API, UI, implementation plan, testing).

This keeps each feature independently understandable: an AI tool working on the `orders` module should only need `1-project`, the global standards (`2-database`, `3-api`, `4-ui`), and `5-modules/orders/` — not the entire documentation set.

See `5-modules/README.md` for the full module workflow, document responsibilities table, and `prompts/3-document-generate/05-modules/modules.md` for how a module's 11 documents actually get generated (just-in-time, per the trigger described above).

---

## How an AI Tool Should Use This Folder

This folder is read by the generation prompts, not read directly by a developer starting fresh — start at `project-docs/prompts/README.md` instead, which drives the real workflow. For understanding what a specific already-generated document should contain, or when writing/reviewing that document:

1. Read this README for context on where the document fits.
2. Read `docs-kit/1-project/` fully (overview → requirements → feature breakdown → tech stack) if not already read this session.
3. Read the relevant global standards for the task at hand:
   - Touching data? Read `docs-kit/2-database/`.
   - Touching an endpoint? Read `docs-kit/3-api/`.
   - Touching a screen? Read `docs-kit/4-ui/`.
4. If working on a specific feature, read that feature's folder under `docs-kit/5-modules/` in full, plus `docs-templates/5-modules/README.md` for the module workflow.
5. Read the relevant parts of `docs-kit/6-development/` for coding standards, folder structure, git workflow, and testing strategy before implementing.
6. Follow each template's own **AI Generation Notes** section when filling in or extending that document.
7. Keep all documents **consistent with each other** — never contradict an already-approved decision in another file; update related docs if a change ripples across them.

---

## Naming Conventions

- Folders and files are **numbered** to indicate reading/build order (`1-`, `2-`, `3-`...).
- File and folder names are **lowercase, kebab-case** (e.g. `tech-stack.md`, `sales-order/`).
- Module folders under `5-modules/` are named after the business capability, singular or plural as natural (e.g. `authentication`, `users`, `orders`).

---

## Status

These are **empty templates** — no project-specific content lives here, ever. Real content is generated per-project into `project-docs/claude-docs/drafts/` then `project-docs/approved-docs/docs-kit/` by the prompts under `project-docs/prompts/3-document-generate/`, driven by `project-docs/prompts/README.md`.

**Framework Version:** tracks `project-docs/README.md`'s own changelog — this file describes the blueprints, not the workflow version; see that changelog for what actually changed and when.
**Document Type:** Docs-templates blueprint index (read-only)
**Applies To:** `project-docs/docs-templates/` only
