# workspace-member

A user's role and membership status within a workspace.

## Purpose

Bind a platform `user` to a `workspace` with a specific role and permission set; drive authorization decisions everywhere.

## Responsibilities

- Invite, accept, revoke, and update memberships.
- Assign roles (`owner`, `admin`, `staff`, `viewer`, custom).
- Resolve effective permissions for a user in a workspace.

## Data model

- `workspace_member`: `id`, `workspace_id`, `user_id`, `role`, `permissions_json?`, `status` (`invited`, `active`, `suspended`, `revoked`), `invited_by`, `invited_at`, `joined_at?`.

Unique on (`workspace_id`, `user_id`).

## Public API

- `workspace_member.invite({ workspace_id, email, role })`
- `workspace_member.accept(token)`
- `workspace_member.update(id, { role?, permissions? })`
- `workspace_member.revoke(id)`
- `workspace_member.can(workspace_id, user_id, action)` — authorization check.

## Events emitted

- `workspace_member.invited`, `workspace_member.joined`, `workspace_member.role_changed`, `workspace_member.revoked`.

## Depends on

- `_platform/workspace`, `_platform/user`.
- `_internal/token` (invite tokens), `_internal/audit`.

## Consumed by

- Every `_workspace` module performing an auth check.
- `_workspace/schedule` (which staff can be booked).
