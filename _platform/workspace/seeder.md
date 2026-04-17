# workspace-seeder

Seeds a new workspace with default data on creation.

## Purpose

Keep the zero-state of a workspace consistent and useful: default taxonomy, sample catalog, starter forms, baseline AI persona.

## Responsibilities

- React to `workspace.created` and run a seed plan.
- Ship multiple seed profiles (minimal, full, industry-specific).
- Be idempotent: running twice on the same workspace is a no-op.

## Data model

- `workspace_seed_run`: `id`, `workspace_id`, `profile`, `status`, `started_at`, `finished_at?`, `error?`.

## Public API

- `workspace_seeder.run({ workspace_id, profile })`
- `workspace_seeder.preview(profile)` — describes what a profile would create.

## Events emitted

- `workspace_seeder.started`, `workspace_seeder.finished`, `workspace_seeder.failed`.

## Depends on

- Every `_workspace` module it seeds into (`taxonomy`, `catalog`, `form`, `ai-persona`, …).

## Consumed by

- `_platform/workspace` on creation.
- Admin tooling (re-run with a different profile on dev workspaces).
