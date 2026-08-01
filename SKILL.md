---
name: mathlib-definition
description: Evidence-driven workflow for designing a good Mathlib definition. Use when adding a new structure/def intended for mathlib, choosing between design alternatives (index type as field vs parameter, Set vs indexed family, data vs Prop, instance vs hypothesis, existential shape), justifying a design in PR review, or when asked to "check the precedents" for a definition or to build a field-vs-parameter-style example sheet. Excavates mathlib's own history (source survey, git archaeology, PR trail, Zulip), maps each design axis to its downstream consequences, and produces a compiling contrasting-cases example sheet in which every claim is demonstrated by a real goal, a real error, or a real historical diff — and every quote is source-pinned.
---

# Designing a Mathlib definition

A mathlib definition is an interface contract paid for by every future user. The
core insight of this workflow: **mathlib has already run the experiment for most
design choices** — in its git history, its PR reviews, and its Zulip threads.
Your job is to excavate that experiment, not to re-argue it from first
principles; and then to *demonstrate* the result with compiling code, not to
assert it with prose.

The workflow was distilled from the `Group.Generators` / `Group.Presentation`
design project (branch `Presentation` in the `Group.Presentation` mathlib
checkout, 2026-07-19 → 2026-07-31; exemplar artifact:
`scratch_field_vs_parameter.lean` in that project root). Its case-study
evidence is threaded through the reference files with citations.

## Outputs

The workflow produces three artifacts, in order:

1. **A precedent dossier** — a chat summary (and, for durable conclusions, a
   memory note) of what mathlib tried, what it refactored to, and why, with
   every claim pinned to a PR URL, commit hash, `path:line`, or Zulip message.
2. **The definition itself**, with each field's form chosen by the API that
   will consume it (see "the verb decides the container",
   `references/design-axes.md`).
3. **An example sheet** — a single compiling `.lean` file of contrasting cases
   that lets a reader *watch* each rejected design fail and each chosen design
   succeed, with the real history reconstructed at the end
   (`references/example-sheet.md`).

## Phase 0 — Name the consumers

Before any design choice, write down, for each candidate field, the downstream
API that will consume it (`FreeGroup.lift` wants `α → G`;
`Subgroup.normalClosure` and `PresentedGroup` want `Set (FreeGroup α)`;
instance search wants class arguments on *parameters*). Every later decision is
made by asking what form the consumer demands — "the header is a contract; make
it speak the caller's language" — not by symmetry or aesthetics.

## Phase 1 — Precedent excavation

Follow `references/precedent-excavation.md`. In brief:

1. **Survey live mathlib** for structures of the same shape; read them fully;
   classify each into a design bucket (never-bundled predicate /
   bundled-data-over-parameter-index / fully bundled / existential Prop layer).
2. **Git archaeology** on each precedent: `git log --follow`, pickaxe
   `git log -S "<TODO or docstring text>"` to date the pain,
   `git show <hash>^:<path>` to quote the pre-refactor code,
   `git show --stat` to measure the refactor's blast radius.
3. **PR trail**: `gh pr view <n> --json title,body,comments,reviews` — commit
   messages, reviewer arguments, and `leanprover-bot` benchmark comments
   (performance numbers are design evidence).
4. **Zulip**: the spectator JSON API, not web search (search engines do not
   index recent Zulip). Design authority often lives here, not in the PR.
5. **Direction-of-travel test**: count refactors toward vs away from each
   candidate design, and hunt for *failed reverse experiments* — a refactor
   attempted in the opposite direction and abandoned is the strongest evidence
   there is.
6. **Cost accounting of holdouts**: for every surviving counterexample to the
   trend, name the visible price it pays (linter suppressions, `shrink`
   apparatus, equiv-only APIs, "nonstandard" self-descriptions).
7. **Adversarial verification**: every claimed precedent is re-checked against
   the actual source before it enters the dossier. Expect refutations; in the
   case study 3 of 49 claims died on verification, and one long-lived citation
   error (#4949 vs #7496) was only caught by re-fetching the PR.

Deliver the dossier as a chat summary before touching the definition.

## Phase 2 — Decide each axis, then stress-test

Work through the checklist in `references/design-axes.md` (field vs parameter,
family vs Set, data vs Prop, primary-Prop choice, existential shape, finiteness
form, `extends` vs indexed-by-term, placement). For each axis record: the
alternatives, the consumer-driven argument, the precedent evidence, and the
*honest flip side* — what the losing design does genuinely better. A dossier
with no flip sides is advocacy, not analysis.

Then stress-test before committing:

- **Simulate variants in scratch**, never in the repo — build the alternative
  live (`lean_run_code`, or a scratch file in the project root so the IDE
  elaborates it) and run the same probes against both.
- **Dogfood**: build a small consumer API on top of the candidate definition
  (in the case study: Tietze transformations, then a Dehn-function API). The
  definition's defects surface as friction in the consumer's proofs.
- **Process review feedback analysis-first**: when reviewers push back,
  analyze the feedback against the consumer principle before writing code —
  and be willing to find that a reviewer's suggestion "quietly reintroduces
  the exact problem you just agreed was decisive."
- **Regression check**: "Is any consumer made worse off?" A change is safe
  when the old form is recoverable by a total conversion at the use site.

## Phase 3 — The example sheet

Follow `references/example-sheet.md`. One compiling `.lean` file that argues
the design by contrasting cases: minimal structure twins differing in exactly
one design choice and named with short caps style labels (e.g. FIELD vs
PARAM), numbered tests each stating a meaningful proposition in plain
English and chunked per style by bold separator blocks (`/-! **FIELD** -/`
/ escape hatches / `/-! **PARAM** -/` — the separators are the only place
the labels are bolded; `###` headers stay reserved for unique titles so the
outline remains useful navigation), deliberate failures at labeled
`EXPECTED ERROR`
examples with verbatim compiler text, escape hatches placed directly under
the failure they rescue and labeled honestly, a reconstruction of the real
pre-refactor API from pinned git history, and a references section mirroring
the argument.
The format is grounded in the learning-science literature (contrasting cases,
analogical encoding, variation theory, erroneous examples, self-explanation,
expertise reversal) — citations and the mapping are in the reference file.

## Phase 4 — The PR justification

When writing the PR description or answering review:

- Cite the **same-subject-area precedent first** (for `Group.Generators`:
  #7698, whose commit message makes the geometric-group-theory argument), the
  cross-area refactors second (#25085, #37928), Zulip authority third
  (the 2021 "Bundled basis" thread), performance numbers fourth.
- Link the example sheet as evidence a reviewer can run.
- Keep the PR minimal: one statement shape per PR, general lemmas upstreamed
  to their natural home file in a separate prerequisite PR, no riders.

## Hard rules (all phases)

- **Always cite the source.** Every quote, reconstruction, or historical
  reference carries `path:line`, a commit-pinned form
  (`git show <hash>^:<path>`) for historical content, and the PR/Zulip URL.
  Citing a source obliges quoting it *exactly* — verbatim attributes,
  signatures, and punctuation. Verify quotes against the source before
  writing them, not after.
- **Truth is the floor, not a slider.** A statement can be true and still
  misleading (a `Set G` that "enters the statement only as an index type");
  reject misleading forms even when technically correct. Never overstate a
  failure — if a rejected design's proof happens to work, say so and rewrite
  the test to show the real pain.
- **Demonstrations must be honest.** An escape hatch must recover the tool
  under test; a workaround that sidesteps the tool stays only if labeled as a
  contrast case.
- **Comment style**: all free commentary in `/- ... -/` blocks (docstrings
  `/-- -/`, module docs `/-! -/`); never `--` line comments.
- **Compile contract**: an example sheet compiles with *exactly* the intended
  deliberate errors at labeled `EXPECTED ERROR` examples and zero warnings;
  re-verify the full diagnostic set after every edit.
- Relators-adjacent docstrings say "evaluates to the identity in `G`", never
  "dies in `G`".
