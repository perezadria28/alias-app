# ADR 0002 — Use uv for Python tooling

- **Status:** Accepted
- **Author:** perezadria00
- **Date:** 2026-07-02

## Context

`alias-app` will use Python for the core generator, local backend, and TUI. The
project needs a repeatable way to manage Python versions, dependencies,
packaging, and test commands without adding unnecessary ceremony.

The main options are `uv`, Poetry, and `venv` plus `pip`. The user already
prefers `uv` for speed and convenience, and the project is new enough that there
is no migration cost.

Good for this decision means:

- fast local setup;
- reproducible dependency resolution;
- simple commands for tests and package work;
- enough structure for multiple Python packages later;
- minimal maintenance overhead.

## Decision

### 1. Use uv as the Python tooling default

The project MUST use `uv` as the default tool for Python environment and
dependency management.

Future Python packages SHOULD be created in a way that works naturally with
`uv` commands and lockfiles.

### 2. Start with the core package only

The first Python implementation slice MUST focus on `packages/core`. Backend and
TUI packages SHOULD be added only after the core behaviour is tested.

### 3. Keep frontend tooling separate

The React + TypeScript web app MAY use its own JavaScript tooling later. This
ADR only decides Python tooling.

## Consequences

### Positive

- `uv` gives fast dependency installation and environment management.
- The project starts with modern tooling and no legacy migration burden.
- Python commands can become reproducible early.
- The tooling can support multiple Python packages as the project grows.

### Negative

- Contributors without `uv` installed need one extra setup step.
- `uv` is newer than Poetry or raw `pip`, so some workflows may be less familiar.
- The project still needs separate decisions for JavaScript tooling and monorepo
  orchestration.

## Confirmation

This decision is confirmed when:

- the Python project files are created using `uv`-compatible metadata;
- dependency lock state is reproducible;
- core package tests can be run through a documented `uv` command.

## Affected Areas

| Area | Impact | Description |
|------|--------|-------------|
| `packages/core` | Future | First Python package to be created using `uv` tooling. |
| `packages/backend` | Future | Should follow the same Python tooling decision later. |
| `packages/tui` | Future | Should follow the same Python tooling decision later. |
| `README.md` | Future | Should document setup commands once README is created. |

## Rollback Plan

If `uv` becomes a poor fit, write a new ADR that supersedes this one and migrate
Python project metadata to Poetry or `venv` plus `pip`. Because no runtime code
exists yet, rollback is currently limited to changing documentation and future
bootstrap commands.

## Alternatives considered

| Alternative | Why not |
|---|---|
| **Poetry** | Mature and capable, but heavier and slower for this new project. |
| **venv + pip** | Standard and simple, but less convenient for repeatable multi-package workflows. |
| **Defer tooling decision** | Would delay the first core implementation without much benefit because the user already prefers `uv`. |

## Open questions

1. Should the repository use one root Python workspace or separate package-level
   project files for `core`, `backend`, and `tui`?
2. Which test runner should the core package use first?
