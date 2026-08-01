---
name: mathlib-definition
description: Evidence-driven workflow for designing a good Mathlib definition. Use when adding a new structure/def intended for mathlib, choosing between design alternatives (index type as field vs parameter, Set vs indexed family, data vs Prop, instance vs hypothesis, existential shape), justifying a design in PR review, or when asked to "check the precedents" for a definition or to build a field-vs-parameter-style example sheet. Clarifying and follow-up questions about a design explanation are answered via the companion adaptive-teacher skill.
---

# Designing a Mathlib definition

A mathlib definition is an interface contract paid for by every future
user. The core insight of this workflow: **mathlib has already run the
experiment for most design choices** — in its git history, its PR reviews,
and its Zulip threads. Excavate that experiment instead of re-arguing it
from first principles; then *demonstrate* the result with compiling code,
not prose.

Load each reference file when its phase begins — nothing else up front.
(Workflow distilled from the `Group.Generators` / `Group.Presentation`
design project, 2026-07; exemplar artifact:
`scratch_field_vs_parameter.lean` in that project root.)

## Outputs, in order

1. **A precedent dossier** — chat summary (durable conclusions also go to a
   memory note) of what mathlib tried, what it refactored to, and why,
   every claim pinned to a PR URL, commit hash, `path:line`, or Zulip
   message.
2. **The definition itself**, each field's form chosen by the API that will
   consume it ("the verb decides the container").
3. **An example sheet** — one compiling contrasting-cases `.lean` file in
   which the reader *watches* each rejected design fail and each chosen
   design succeed.

## Phase 0 — Name the consumers

Before any design choice, write down, for each candidate field, the
downstream API that will consume it (`FreeGroup.lift` wants `α → G`;
`Subgroup.normalClosure` and `PresentedGroup` want `Set (FreeGroup α)`;
instance search wants class arguments on *parameters*). Every later
decision asks what form the consumer demands — "the header is a contract;
make it speak the caller's language" — never symmetry or aesthetics.

## Phase 1 — Precedent excavation

Follow `references/precedent-excavation.md` end to end: survey live
mathlib and classify precedents into its four buckets; git archaeology
(pickaxe-dating, pre-refactor quotes via `git show <hash>^:<path>`, blast
radius); the PR trail via `gh` (reviewer arguments and benchmark numbers
are design evidence); Zulip via the spectator JSON API (web search does
not index recent Zulip); the direction-of-travel count including failed
reverse experiments; cost accounting of holdouts; and adversarial
verification of every claimed precedent against source — in the case study
3 of 49 claims died there. Deliver the dossier as a chat summary before
touching the definition.

## Phase 2 — Decide each axis, then stress-test

Work the checklist in `references/design-axes.md`; for each axis record
the alternatives, the consumer-driven argument, the precedent, and the
*honest flip side* — a dossier with no flip sides is advocacy, not
analysis. Then stress-test before committing:

- **Simulate variants in scratch**, never in the repo — build the
  alternative live (`lean_run_code`, or a scratch file in the project root
  so the IDE elaborates it) and run the same probes against both.
- **Dogfood**: build a small consumer API on top of the candidate
  definition; its defects surface as friction in the consumer's proofs.
- **Process review feedback analysis-first**: check a reviewer's
  suggestion against the consumer principle before writing code — it may
  quietly reintroduce the exact problem just agreed decisive.
- **Regression check**: "Is any consumer made worse off?" A change is safe
  when the old form is recoverable by a total conversion at the use site.

## Phase 3 — The example sheet

Follow `references/example-sheet.md` (the recipe and the compile/citation
contracts; the learning-science grounding behind the format is split into
`references/example-sheet-evidence.md`, loaded only when the format itself
is questioned or amended). In one line: minimal structure twins differing
in exactly one design choice, numbered tests chunked per caps-labeled
style, deliberate failures at labeled `EXPECTED ERROR` examples with
verbatim compiler text, escape hatches priced honestly, the real
pre-refactor API reconstructed from pinned git history, and a references
section mirroring the argument.

## Phase 4 — The PR justification

- Citation order: same-subject-area precedent first (for
  `Group.Generators`: #7698, whose commit message makes the
  geometric-group-theory argument), cross-area refactors second (#25085,
  #37928), Zulip authority third (the 2021 "Bundled basis" thread),
  performance numbers fourth.
- Link the example sheet as evidence a reviewer can run.
- Keep the PR minimal: one statement shape per PR, general lemmas
  upstreamed to their natural home file in a separate prerequisite PR, no
  riders.

## Companion skill — clarifying questions (all phases)

When the user asks a clarifying or follow-up question while this skill is
active, invoke `adaptive-teacher`: it reads its learner model first,
answers per its protocol, and logs the question. Two obligations flow
back: compress in dossiers and example sheets whatever its learner model
marks known-well, and when a logged question reveals a *user-independent*
defect in this skill's reference text, fix the reference file here and
commit, citing the log entry date.

## Hard rules (all phases)

- **Always cite the source.** Every quote, reconstruction, or historical
  reference carries `path:line`, a commit-pinned form
  (`git show <hash>^:<path>`) for historical content, and the PR/Zulip
  URL. Citing obliges quoting *exactly* — verbatim attributes, signatures,
  and punctuation; verify quotes against the source before writing them,
  not after.
- **Truth is the floor, not a slider.** A statement can be true and still
  misleading; reject misleading forms even when technically correct. Never
  overstate a failure — if a rejected design's proof happens to work, say
  so and rewrite the test to show the real pain.
- **Demonstrations must be honest.** An escape hatch must recover the tool
  under test; a workaround that sidesteps the tool stays only if labeled
  as a contrast case.
- **Comment style**: all free commentary in `/- ... -/` blocks (docstrings
  `/-- -/`, module docs `/-! -/`); never `--` line comments.
- **Compile contract**: an example sheet compiles with *exactly* the
  intended deliberate errors at labeled `EXPECTED ERROR` examples and zero
  warnings; re-verify the full diagnostic set after every edit.
- Relators-adjacent docstrings say "evaluates to the identity in `G`",
  never "dies in `G`".
