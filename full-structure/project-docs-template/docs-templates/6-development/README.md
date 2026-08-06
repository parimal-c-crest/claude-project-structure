# 6-development

> **Purpose**
>
> Defines how the project is actually built, tested, and shipped day-to-day — local environment setup, repo conventions, coding practices, git workflow, testing, containerization, CI/CD, and debugging. This is where documentation ends and implementation begins.

Templates live in `templates/`. Requires `1-project/`, `2-database/`, `3-api/`, and `4-ui/` to be understood first; this folder governs *how* those get implemented, not *what* gets built.

## Contents

| # | File | Covers |
|---|---|---|
| 1 | `templates/1-development-environment.md` | Required software, tooling, installation, and configuration for a consistent local setup. |
| 2 | `templates/2-folder-structure.md` | Standard project directory structure and ownership across the codebase. |
| 3 | `templates/3-coding-standards.md` | Coding conventions and quality requirements all developers/AI tools must follow. |
| 4 | `templates/4-git-workflow.md` | Branching model, commit conventions, PR process, merge strategy. |
| 5 | `templates/5-implementation-workflow.md` | Step-by-step lifecycle for building a feature from approved requirements to done. |
| 6 | `templates/6-testing-strategy.md` | Testing levels, quality objectives, responsibilities, and automation approach. |
| 7 | `templates/7-deployment-strategy.md` | Release process, environment management, rollback procedures. |
| 8 | `templates/8-containerization.md` | Docker architecture, image management, container lifecycle, networking. |
| 9 | `templates/9-ci-cd.md` | CI/CD pipeline architecture, automation, and quality gates. |
| 10 | `templates/10-debugging-guide.md` | Troubleshooting methodology, diagnostic tools, root cause analysis. |

## Before You Start

Read `5-implementation-workflow.md` first — it ties the rest of this folder together into the actual day-to-day process for building a feature. The threat model (attack surface, threats, mitigations) lives in `7-cross-cutting/2-threat-model.md`, generated last since it cross-checks decisions made everywhere else, including this folder's own coding standards.
