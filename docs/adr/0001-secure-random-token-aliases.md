# ADR 0001 — Secure random token aliases

- **Status:** Accepted
- **Author:** perezadria00
- **Date:** 2026-07-02

## Context

`alias-app` needs a default alias format that works across providers without
depending on provider APIs. The user prioritises likely uniqueness first,
avoiding personal or sensitive semantic patterns second, reducing local traces
third, and readability fourth.

The expected MVP workflow is copy/paste into a password manager such as
KeePassXC. The MVP therefore optimises for privacy and entropy over
memorability or typing comfort.

Provider services such as Addy.io and SimpleLogin can guarantee availability
inside their own namespaces because they own those namespaces. A local
provider-agnostic generator cannot guarantee that an alias is available at
Tuta, SimpleLogin, Addy.io, Gmail, iCloud, or any other provider.

Good for this decision means:

- low practical collision risk for personal use;
- no dictionary words, names, or personal data by default;
- broad compatibility with email local-part rules;
- a clear separation between the canonical alias value and how interfaces may
  display it.

## Decision

### 1. Use compact secure-random tokens as the canonical value

The default alias candidate MUST be a compact token generated with
cryptographically secure randomness.

The default character set MUST be lowercase ASCII letters and digits:
`abcdefghijklmnopqrstuvwxyz0123456789`.

The default token length MUST be 16 characters for the MVP.

The canonical value MUST NOT include separators, spaces, dictionary words,
human names, provider names, or user-provided personal labels.

### 2. Keep visual grouping separate from the canonical value

Interfaces MAY display the canonical value in visual groups to improve
readability.

Copy, export, API responses, tests, and core package behaviour MUST use the
compact canonical value without visual separators.

Example:

```text
canonical value: k7q9m2x8v4zn8p2r
visual display:  k7q9 m2x8 v4zn 8p2r
copied value:    k7q9m2x8v4zn8p2r
```

### 3. Do not claim provider availability

The app MUST present generated aliases as candidates. It MUST NOT claim that a
candidate is available at any provider unless a future provider integration
explicitly checks that provider.

### 4. Do not persist generated aliases by default

Generated aliases MUST NOT be stored on disk by default. Interfaces MAY keep
generated aliases in local memory for the current app session and MAY remove
them from visible state after a configurable TTL.

## Consequences

### Positive

- The default format minimises semantic leakage because aliases do not contain
  words, names, provider identifiers, or personal labels.
- The compact canonical value is likely to be accepted by many providers because
  it avoids symbols and separators.
- Visual grouping can improve readability without changing the value copied or
  used by providers.
- The app is honest about its limits: providers remain the authority for actual
  availability.

### Negative

- Random tokens are harder to remember than word-based aliases.
- The app cannot guarantee global uniqueness or provider availability.
- Some providers may impose local-part restrictions that are narrower than this
  app's default rules.
- Visual grouping creates a UI responsibility: interfaces must not accidentally
  copy grouped separators.

## Confirmation

This decision is confirmed when tests prove that:

- default aliases contain only lowercase ASCII letters and digits;
- default aliases do not include separators;
- default aliases are generated through a secure random source;
- formatting for display does not mutate the canonical copied value;
- generated aliases are not persisted by default.

Manual UI checks should confirm that grouped display and copied value differ only
by visual separators.

## Affected Areas

| Area | Impact | Description |
|------|--------|-------------|
| `docs/specs/0001-alias-candidate-generator.md` | New | Defines observable behaviour for alias candidate generation. |
| `packages/core` | Future | Will implement canonical generation, validation, and display formatting helpers. |
| `packages/backend` | Future | Must return canonical values, not grouped display strings. |
| `packages/tui` | Future | May show visual grouping but must copy canonical values. |
| `packages/web` | Future | May show visual grouping but must copy canonical values. |

## Rollback Plan

If compact random tokens are a poor default, write a new ADR that supersedes this
one and changes the default strategy. Keep existing generated aliases unchanged
because they are not persisted by default.

Implementation rollback would affect the future core generator defaults and any
UI display helpers. Since this decision currently creates documentation only,
rollback means superseding this ADR and updating the related spec if behaviour
changes.

## Alternatives considered

| Alternative | Why not |
|---|---|
| **Dictionary word aliases** | Easier to read, but they leak semantic patterns and may include sensitive or unwanted words. |
| **Provider-specific aliases** | Could fit one provider better, but would tie the default product to Tuta, SimpleLogin, Addy.io, or another provider too early. |
| **Canonical aliases with hyphen separators** | More readable, but less provider-agnostic because not every provider accepts the same separators. |
| **UUID aliases** | Strong uniqueness properties, but longer and less pleasant for manual copying. |

## Open questions

1. Should future versions support custom formats such as word-based or
   provider-specific aliases?
2. Should provider compatibility presets be added later?
3. What TTL should interfaces use if they show a temporary alias list?
