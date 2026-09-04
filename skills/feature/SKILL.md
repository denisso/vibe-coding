`---
name: requirement-scenario-spec
description: >
  Converts requirements given in any input format (free prose, bullet notes,
  user stories, Jira-style tickets, raw Gherkin, voice-transcribed notes,
  half-formed ideas) into the OpenCode/OpenSpec-style structured markdown
  requirement template: a "### Requirement:" heading with an RFC 2119
  SHALL/SHALL NOT statement, followed by one or more "#### Scenario:"
  headings using bold GIVEN/WHEN/THEN/AND bullets, where GIVEN is included
  only for scenarios that have a real precondition distinct from their
  trigger (decided automatically per scenario, not by the user), with a
  stable slug ID minted for each scenario so it can later be referenced from
  code comments. Trigger
  whenever the user asks to "format this as a requirement/spec", "turn this
  into OpenCode/OpenSpec format", references the Requirement/Scenario
  template, or pastes raw requirements/notes and asks for them structured as
  SHALL-based requirements with scenarios — regardless of what format the
  input is in.
---

# Requirement/Scenario Markdown Spec

Converts requirements — in whatever form the user hands them over — into the OpenCode/OpenSpec canonical spec format: `### Requirement:` blocks with RFC 2119 modal language, each followed by `#### Scenario:` blocks with WHEN/THEN/AND bullets.

## Output template (exact shape, do not deviate)

```markdown
### Requirement: <Short capability name, noun phrase, not a full sentence>

<One to three sentences, third person, describing the behavior. Use SHALL for
mandatory behavior, SHALL NOT for prohibited behavior, MAY only for genuinely
optional behavior. SHALL/SHALL NOT/MAY are plain text here — not bolded.>

#### Scenario: <Short scenario title describing this specific branch>

**Scenario ID:** `<scenario-id>`

- **GIVEN** <starting state/precondition — include only when this scenario has one distinct from its trigger; omit the line entirely otherwise, decided per scenario>
- **WHEN** <trigger or precondition (default style) / triggering action (GIVEN style), present tense>
- **AND** <additional precondition, only if there's a second condition — omit if not needed>
- **THEN** <required outcome, using SHALL / SHALL NOT>
- **AND** <additional outcome, repeat as needed>
```

Repeat `#### Scenario:` for every distinct behavioral branch under that requirement, then move to the next `### Requirement:` block for the next distinct capability.

## Hard formatting rules

1. **Default style: GIVEN / WHEN / THEN / AND, decided per scenario.** Unlike the raw OpenCode/openspec reference (which only ever showed WHEN/THEN/AND), this skill's default includes `GIVEN` as an available keyword — but only writes it into a given scenario when that scenario actually has a distinct starting state/precondition separate from its triggering action. Decide this per scenario, automatically, based on what the input implies — don't ask the user, don't apply it uniformly to every scenario, and don't force a `GIVEN` line when the "precondition" and the trigger are really the same thing (e.g. "the browser is offline" is the trigger itself, not a separate state to set up first — that scenario stays WHEN-only even though GIVEN is available). If the user explicitly asks to match the exact reference format with no GIVEN at all, drop GIVEN entirely for that output instead. Still don't add `BUT`, `Background:`, `Scenario Outline:`, or `Examples:` — those remain out of scope regardless of style.
2. **Bold only the keyword**, via `**WHEN**`, `**THEN**`, `**AND**` — never bold SHALL/SHALL NOT/MAY, never bold the whole line.
3. Every `THEN` and every outcome `AND` should contain a SHALL / SHALL NOT (rarely MAY) — that's what makes it a verifiable requirement rather than a description.
4. **Preserve concrete specifics verbatim** — exact routes (`/goodbye`), header names (`X-Preferred-Locale`), status codes, field names, locale codes, error strings. Never generalize or paraphrase away specifics that were in the source input.
5. Requirement titles are short noun phrases ("Signed-in user can permanently delete their account"), not full SHALL-sentences — the SHALL-sentence goes in the body paragraph below it.
6. Scenario titles are short and describe the specific branch ("Confirmed deletion removes account and redirects", "Deletion requires online state") — distinguish happy path from edge/error cases in the title itself.
7. **Every scenario gets a stable slug ID**, placed as the first line of the scenario body, directly under the heading and before the WHEN/THEN/AND bullets: `**Scenario ID:** \`<id>\``. Keep the `#### Scenario: <title>` heading itself exactly as it would appear without an ID — the ID line is purely additive, so the heading stays identical to what a plain OpenCode/openspec-generated file would show (avoids breaking any tooling that parses the heading as just the title). This ID is a join key with code — a separate step (not part of this skill) will later insert this exact string as a comment in the code that implements the scenario, so it MUST be copy-pasteable as one unbroken token and MUST NOT be regenerated once minted (see "Scenario ID minting" below).

## Scenario ID minting

**Format:** `<capability-slug>.<requirement-slug>.<scenario-slug>` — three dot-separated kebab-case segments, each 2–4 words, all lowercase, no spaces.

- `capability-slug` — one slug for the whole file/feature (same value for every requirement and scenario in this output), e.g. `account-deletion`.
- `requirement-slug` — short slug of that Requirement's title, e.g. `delete-account`, `email-confirmation`, `goodbye-page`.
- `scenario-slug` — short slug of that Scenario's title, e.g. `confirmed-redirect`, `requires-online`.

Full example: `account-deletion.delete-account.confirmed-redirect`

**Stability rules:**
- Mint the ID in the same pass that creates the scenario — never leave a scenario without one, even temporarily.
- Once minted (in this output or in a spec file the user pastes back in for editing), an ID is permanent: if the user asks to reword a Requirement or Scenario title afterward, keep its existing ID unchanged. Only mint a new ID for a genuinely new scenario.
- If a new scenario's slug would collide with an ID already present in the same file (including ones the user pasted in as existing content to extend), disambiguate by appending `-2`, `-3`, etc. to the scenario-slug segment.
- If the user provides an existing spec file to add requirements/scenarios to, scan it for already-minted IDs first and treat those as fixed — don't touch them, and check new IDs against them for collisions.

## Turning arbitrary input into this shape

Since the input format is never fixed, work in two passes:

**Pass 1 — cluster into Requirements.** Read the input (prose, notes, tickets, Gherkin, whatever) and group it into distinct capabilities — each thing that could stand alone as one mandatory system behavior gets its own `### Requirement:`. Don't split one coherent capability into multiple requirements, and don't merge two unrelated capabilities into one.

**Pass 2 — derive Scenarios per requirement.** For each requirement, identify the distinct branches actually implied by the input:
- the main/happy path,
- explicitly stated alternate conditions (offline, wrong input, missing header, etc.),
- explicitly stated error/guard conditions.

Only create a scenario for a branch the input actually implies — do not invent edge cases, error states, or fields that weren't mentioned or reasonably implied, even though the worked example below happens to have several. If the input is thin (e.g. one sentence, one bullet), it's fine to produce a single Requirement with a single Scenario. If something important seems ambiguous or missing (e.g. no stated behavior for a failure case that obviously exists), say so briefly in the chat reply rather than fabricating a scenario for it.

## Where to save

Save as a new file at `/mnt/user-data/outputs/<slug>.md`, where `<slug>` is a kebab-case slug of the overall feature/capability being specified (ask the user for a short feature name if it's not obvious from the input, otherwise infer it). Then call `present_files`. Keep the chat reply short — one line, plus any note about ambiguous/missing branches from Pass 2.

## Worked example

**Input (unstructured notes, hypothetical):**
> users should be able to delete their account from settings. needs typed email confirmation matching their account email before the delete button works. only works online. after deleting, wipe their CRDT data and sessions, send them to a signed-out /goodbye page, no account recovery offered there. also send them an email confirming deletion, translated based on their locale header, falling back to sr-Latn if not set.

**Output** (abbreviated to one requirement for illustration — the full multi-requirement version follows the same pattern as shown in the reference example this skill was built from):

```markdown
### Requirement: Signed-in user can permanently delete their account

The system SHALL allow a signed-in online user to permanently delete their own account from Account settings. Deletion SHALL remove the auth account, active sessions, server-synced CRDT data, and local user-specific browser data.

#### Scenario: Confirmed deletion removes account and redirects

**Scenario ID:** `account-deletion.delete-account.confirmed-redirect`

- **GIVEN** a signed-in online user viewing Account settings
- **WHEN** the user confirms account deletion with the required email confirmation
- **THEN** the system SHALL delete the signed-in user's account
- **AND** the user's server-synced CRDT data SHALL be deleted
- **AND** the user's authenticated session SHALL be cleared
- **AND** the browser SHALL navigate to the signed-out goodbye page

#### Scenario: Deletion requires online state

**Scenario ID:** `account-deletion.delete-account.requires-online`

- **WHEN** account deletion is requested while the browser is offline
- **THEN** the system SHALL NOT attempt the deletion request
- **AND** the UI SHALL communicate that account deletion requires internet access
```

Note the two scenarios were decided differently, automatically: the first has a real precondition ("signed-in, viewing Account settings") distinct from the trigger ("confirms deletion"), so it gets a `GIVEN` line. The second has no separate precondition — "the browser is offline" *is* the trigger — so it stays WHEN-only even though `GIVEN` is available by default. This decision is made per scenario while generating, not toggled by the user and not applied uniformly across the file.

Also note how "typed email confirmation," "/goodbye," and "locale fallback to sr-Latn" would each become their own `### Requirement:` block in the full output (as they did in the reference example), each with its own `email-confirmation`, `goodbye-page`, `deletion-email` requirement-slug feeding into its scenarios' IDs — rather than being folded into the `delete-account` requirement above.
