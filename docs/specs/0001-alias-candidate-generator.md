# Spec 0001 — Alias candidate generator

- **Status:** Accepted
- **Author:** perezadria00
- **Date:** 2026-07-02
- **Related:** [ADR-0001](../adr/0001-secure-random-token-aliases.md)

## Requirements

### Requirement: Generate provider-agnostic alias candidates

The system MUST generate alias candidates locally without depending on a
specific email provider.

#### Scenario: Alias candidate is generated locally

- GIVEN the user requests a new alias candidate
- WHEN the generator runs
- THEN it returns a local-part string
- AND it does not contact any external service

---

### Requirement: Use secure randomness

The system MUST use cryptographically secure randomness for generated alias
candidates.

#### Scenario: Generator uses secure randomness

- GIVEN the generator is configured with the default random strategy
- WHEN it creates an alias candidate
- THEN the candidate is derived from a cryptographically secure random source

---

### Requirement: Avoid personal or semantic content by default

The system MUST NOT include dictionary words, names, user-provided personal
data, or provider names in the default alias format.

#### Scenario: Default alias contains only neutral characters

- GIVEN the user requests a default alias candidate
- WHEN the generator returns the candidate
- THEN the candidate contains only allowed neutral characters
- AND it does not contain dictionary words or personal labels

---

### Requirement: Make collisions unlikely

The system MUST generate alias candidates with enough entropy to make collisions
unlikely for normal personal use.

#### Scenario: Default alias has minimum entropy

- GIVEN the default generator configuration
- WHEN an alias candidate is generated
- THEN the candidate uses at least the configured minimum random length
- AND the character set provides enough combinations for low collision risk

---

### Requirement: Do not claim provider availability

The system MUST NOT claim that an alias candidate is available at any email
provider.

#### Scenario: Alias candidate is presented as unchecked

- GIVEN an alias candidate has been generated
- WHEN it is displayed to the user
- THEN the system communicates that provider availability is not checked
- AND the provider remains the authority that accepts or rejects the alias

---

### Requirement: Do not persist generated aliases by default

The system MUST NOT store generated aliases on disk by default.

#### Scenario: Alias disappears after process exit

- GIVEN aliases were generated during an app session
- WHEN the app process exits
- THEN generated aliases are not available from app storage on next startup

---

### Requirement: Expire aliases from local UI state

The system MAY expire generated aliases from local UI state after a
configurable TTL.

#### Scenario: Alias expires after TTL

- GIVEN an alias candidate is visible in the local app state
- AND a TTL is configured
- WHEN the TTL elapses
- THEN the alias candidate is removed from the visible local app state

#### Scenario: Alias remains while TTL has not elapsed

- GIVEN an alias candidate is visible in the local app state
- AND a TTL is configured
- WHEN less than the TTL has elapsed
- THEN the alias candidate remains visible

---

### Requirement: Validate generated local parts

The system MUST validate generated alias candidates against the app's local-part
rules before returning them.

#### Scenario: Generated alias matches allowed format

- GIVEN the generator creates an alias candidate
- WHEN validation runs
- THEN the candidate contains only allowed characters
- AND its length is within the configured limits

---

## Out of scope

- Creating aliases inside Tuta, SimpleLogin, Addy.io, or any other provider.
- Checking whether an alias is available at a provider.
- Persisting alias history.
- Managing real email forwarding, inbox rules, recipients, domains, or provider credentials.
- Browser extension behavior.
- Backend, TUI, and web UI layout details.
