---
name: mathlib-api
description: Evidence-driven workflow for designing a Mathlib definition and the API around it. Use when adding a new structure/def intended for mathlib; when choosing between design alternatives (index type as field vs parameter, Set vs indexed family, data vs Prop, instance vs hypothesis, existential shape); when deciding which statements an API should ship and in what form (which Prop is the field, iff vs directional corollaries, simp orientation, smart constructors, bridges to existing idioms, bloat audits); when justifying a design in PR review; or when asked to "check the precedents" for a definition or an API, or to build a contrasting-cases example sheet. Clarifying and follow-up questions about a design explanation are answered via the companion adaptive-teacher skill.
---

# Designing a Mathlib definition and its API

A mathlib definition is an interface contract paid for by every future
user, and the API around it is the rest of that contract: the statements
every consumer will actually call. The core insight of this workflow:
**mathlib has already run the experiment for most design choices** — in
its git history, its PR reviews, and its Zulip threads. Excavate that
experiment instead of re-arguing it from first principles; then
*demonstrate* the result with compiling code, not prose.

Load each reference file when its phase begins — nothing else up front.
(Workflow distilled from the `Group.Generators` / `Group.Presentation`
design project, 2026-07; exemplar artifacts in that project root:
`scratch_field_vs_parameter.lean` for a definition axis,
`scratch_closure_vs_lift.lean` for an API axis.)

## Outputs, in order

1. **A precedent dossier** — chat summary (durable conclusions also go to a
   memory note) of what mathlib tried, what it refactored to, and why,
   every claim pinned to a PR URL, commit hash, `path:line`, or Zulip
   message. Covers both the structure shape and the surface its
   shape-mates ship.
2. **The definition itself**, each field's form chosen by the API that will
   consume it ("the verb decides the container").
3. **The API surface** — each statement admitted with its consumer named,
   stated in the form the consumer demands, attributed (`@[simp]`,
   `@[ext]`, `@[simps]`) deliberately, and audited against bloat.
4. **An example sheet** — one compiling contrasting-cases `.lean` file in
   which the reader *watches* each rejected design fail and each chosen
   design succeed. Two genres: the full sheet with historical
   reconstruction and a closing `/-! ## References -/` dossier section,
   and the lighter API-scratch genre when the precedent is shape-level
   and all pins are local.

## Phase 0 — Name the consumers

Before any design choice, write down the downstream API that will consume
each candidate — for each *field* of the structure (`FreeGroup.lift` wants
`α → G`; `Subgroup.normalClosure` and `PresentedGroup` want
`Set (FreeGroup α)`; instance search wants class arguments on
*parameters*), and for each candidate *statement* the argument slot or
proof site that will call it. Every later decision asks what form the
consumer demands — "the header is a contract; make it speak the caller's
language" — never symmetry or aesthetics.

## Phase 1 — Precedent excavation

Follow `references/precedent-excavation.md` end to end: survey live
mathlib and classify precedents into its four buckets; git archaeology
(pickaxe-dating, pre-refactor quotes via `git show <hash>^:<path>`, blast
radius); the PR trail via `gh` (reviewer arguments and benchmark numbers
are design evidence); Zulip via the spectator JSON API (web search does
not index recent Zulip); the direction-of-travel count including failed
reverse experiments; cost accounting of holdouts; and adversarial
verification of every claimed precedent against source — in the case study
3 of 49 claims died there. When the deliverable includes an API surface,
survey what the shape-mates *ship*, not just how they are declared — the
surface taxonomy to survey against is `references/api-surface.md` §1.
Deliver the dossier as a chat summary before touching the definition.

## Phase 2 — Decide each definition axis, then stress-test

Work the checklist in `references/design-axes.md`; for each axis record
the alternatives, the consumer-driven argument, the precedent, the
*honest flip side*, and the verdict's kind (§0) — a dossier with no flip
sides is advocacy, not analysis, and one that hides a close call behind a
confident verdict is worse, because it looks like analysis. Then
stress-test before committing:

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

## Phase 3 — Build the API surface

Follow `references/api-surface.md`: the standard surface a bundled-data
structure ships (constructors, projection lemmas, ext, transport,
bridges, the existential layer); the per-statement admission tests (a
named consumer, a precedent-standard slot, or a conversion paid once in
the API — otherwise it stays out); statement form (index type over image
set, one simp-normal form, iff vs directional corollaries, weakest
assumptions); and the driver test — prove the downstream targets using
only the proposed API; friction names a missing lemma, and a lemma no
driver uses is a bloat candidate. The same stress-test discipline as
Phase 2 applies: simulate contested statement shapes in scratch before
committing to one.

## Phase 4 — The example sheet

Follow `references/example-sheet.md` (the recipe and the compile/citation
contracts; the learning-science grounding behind the format is split into
`references/example-sheet-evidence.md`, loaded only when the format itself
is questioned or amended). In one line: minimal structure twins differing
in exactly one design choice, numbered tests chunked per caps-labeled
style, deliberate failures at labeled `EXPECTED ERROR` examples with
verbatim compiler text, escape hatches priced honestly, the real
pre-refactor API reconstructed from pinned git history, and the references
dossier as the sheet's closing section, mirroring the argument. For a
contested API choice, the lighter genre in `references/api-surface.md` §5
usually suffices — same contracts, no historical reconstruction.

## Phase 5 — The PR justification

- Citation order: same-subject-area precedent first (for
  `Group.Generators`: #7698, whose commit message makes the
  geometric-group-theory argument), cross-area refactors second (#25085,
  #37928), Zulip authority third (the 2021 "Bundled basis" thread),
  performance numbers fourth.
- Link the example sheet as evidence a reviewer can run.
- Where the axis was a judgment call, the PR says it was one and gives
  the alternative. A reviewer who reopens it is then reopening a question
  the PR already admitted was open, which is cheaper than discovering
  that the confident paragraph was covering a coin flip.
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
- **Report, don't advocate.** Every verdict names its kind — mechanism, a
  count, or a judgment call (`references/design-axes.md` §0). A judgment
  call says so in its first sentence and stops there; it is not settled
  by a tiebreaker invented for the occasion, and "the evidence does not
  separate these" is a complete answer. Before claiming any asymmetry,
  try to erase it by writing the line the other side is missing. Give a
  recommendation only when asked, in one sentence, marked as a
  preference and placed after the evidence.
- **Demonstrations must be honest.** An escape hatch must recover the tool
  under test; a workaround that sidesteps the tool stays only if labeled
  as a contrast case.
- **Scratch files stay unstaged.** Every scratch/example-sheet `.lean` file the
  workflow produces (project-root `scratch*.lean`, the `scratch_*.lean` twins,
  the example sheet itself) is left as an unstaged working-tree change — never
  `git add`ed into the mathlib commit. They are local evidence you keep running
  in the IDE, not part of the PR; leave them out of the staging area so the
  contribution stays minimal.
- **Comment style**: all free commentary in `/- ... -/` blocks (docstrings
  `/-- -/`, module docs `/-! -/`); never `--` line comments.
- **Compile contract**: an example sheet compiles with *exactly* the
  intended deliberate errors at labeled `EXPECTED ERROR` examples and zero
  warnings; re-verify the full diagnostic set after every edit.
- Relators-adjacent docstrings say "evaluates to the identity in `G`",
  never "dies in `G`".
