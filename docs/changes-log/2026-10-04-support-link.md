# 2026-10-04 — Repair the support documentation link

## Why

The support page displayed an unsubstituted repository-name URL and sent readers to a generic personal site.
That link did not identify this repository's documentation.

## What changed

Replaced that placeholder link with [repository documentation](../../docs/README.md). Other support policy and existing
local changes are preserved. This addresses Blueprint debt 39 for this repository.

## Verification

Confirmed the local target `docs/README.md` exists and the support page no longer contains `REPOSITORY-NAME`.
No hosted documentation or external URL availability is claimed.
