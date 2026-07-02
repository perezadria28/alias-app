# alias-app Working Agreement

This file defines project-specific instructions for `alias-app`. The global
OpenCode working agreement still applies.

## Project Goal

`alias-app` is a local, provider-agnostic alias candidate generator.

The app helps users create email alias local parts with high randomness and low
semantic leakage. It does not create aliases inside any email provider and does
not claim provider availability.

## Current Scope

- Generate alias candidates locally.
- Use secure randomness by default.
- Avoid dictionary words, names, provider names, and user personal data by default.
- Keep generated aliases non-persistent by default.
- Allow interfaces to display aliases with visual grouping while copying the compact canonical value.

## Out of Scope For The Initial Slice

- Provider integrations such as Tuta, SimpleLogin, Addy.io, Proton, Gmail, or iCloud.
- Availability checks against provider namespaces.
- Persistent alias history.
- Browser extensions.
- Real email forwarding, inbox rules, recipients, domains, or provider credentials.

## Architecture Direction

- `packages/core` will hold the Python alias generation logic.
- `packages/backend` will expose local HTTP behaviour if needed by the web UI.
- `packages/tui` will provide the Python TUI.
- `packages/web` will provide the React + TypeScript local web UI.
- Shared behaviour MUST live in the core package before being reused by backend, TUI, or web.

## Documentation

- Specs live in `docs/specs/` and follow `~/.config/opencode/templates/spec.md`.
- ADRs live in `docs/adr/` and follow `~/.config/opencode/templates/adr.md`.
- Proposals live in `docs/proposals/` and follow `~/.config/opencode/templates/proposal.md`.
- README is intentionally deferred until the project scope is clearer.

## Project Workflow

- The repository MUST always have a `dev` branch. If `dev` does not exist, the
  workflow is incomplete and must be fixed before non-trivial implementation.
- Non-trivial implementation work SHOULD start from a GitHub issue with clear
  scope and acceptance criteria.
- Feature branches MUST branch from `dev` unless the user explicitly chooses a
  different base.
- Feature PRs MUST target `dev`; do not open feature PRs directly into `main`.
- `main` is reserved for stable promotion or release PRs from `dev`.
- Use milestones for MVP or release grouping.
- Do not introduce formal sprints or project boards unless the active work grows
  beyond a few parallel issues.

## Product Language

- Technical artifacts are written in English.
- User-facing copy for the app should default to English unless explicitly changed later.
- Chat with the user remains in Spanish unless the user requests otherwise.
