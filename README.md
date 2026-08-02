# mathlib-api

A [Claude Code](https://claude.com/claude-code) skill for designing
definitions, and the API around them, intended for
[Mathlib](https://github.com/leanprover-community/mathlib4).
(Formerly `mathlib-definition`; renamed 2026-08-02 when the scope grew
from the definition alone to the full API surface.)

Every design question you hit when writing a definition for mathlib — index
type as a field or a parameter? data or a Prop? which lemmas should ship
with it, and pointing which way? — has usually been answered already,
somewhere in mathlib's git history, PR reviews, or Zulip threads. This
skill digs up those answers instead of arguing from first principles,
then shows them in live Lean code — the rejected design failing with a real
compiler error, the chosen one going through — instead of asserting them in
prose.

## When it triggers

Use it (or let Claude pick it up from the skill description) when:

- adding a new `structure`/`def` intended for mathlib;
- choosing between design alternatives — index type as field vs parameter,
  `Set` vs indexed family, data vs `Prop`, instance vs hypothesis,
  existential shape;
- deciding which statements an API should ship and in what form — which
  Prop is the field, iff vs directional corollaries, simp orientation,
  smart constructors, bridges to existing idioms, bloat audits;
- justifying a design in PR review;
- asked to "check the precedents" for a definition or an API, or to build
  a contrasting-cases example sheet.

## What it produces

1. **A precedent dossier** — what mathlib tried, what it refactored to, and
   why, with every claim pinned to a PR URL, commit hash, `path:line`, or
   Zulip message; covering both the structure shape and the surface its
   shape-mates ship.
2. **The definition itself** — each field's form chosen by the API that will
   consume it ("the verb decides the container").
3. **The API surface** — each statement admitted with its consumer named,
   stated in the form the consumer demands, attributed deliberately, and
   audited against bloat.
4. **An example sheet** — a single compiling `.lean` file of contrasting
   cases: minimal structure twins differing in exactly one design choice,
   numbered tests stating meaningful propositions in plain English,
   deliberate failures at labeled `EXPECTED ERROR` examples with verbatim
   compiler text, escape hatches priced honestly, and a closing
   source-pinned references section. For definition axes with a refactor
   history, the full genre adds the pre-refactor API reconstructed from
   pinned git history; for API choices whose precedent is shape-level, a
   lighter genre usually suffices.

## The workflow

| Phase | What happens | Where |
|---|---|---|
| 0 — Name the consumers | For each candidate field and each candidate statement, write down the downstream API or proof site that will consume it; every later decision asks what form the consumer demands. | [SKILL.md](SKILL.md) |
| 1 — Precedent excavation | Survey live mathlib → git archaeology → PR trail → Zulip → direction-of-travel test → cost accounting of holdouts → adversarial verification of every claim. When an API surface is in scope, survey what the shape-mates ship, not just how they are declared. | [references/precedent-excavation.md](references/precedent-excavation.md) |
| 2 — Decide each definition axis | Work the design-axes checklist (structure vs class, field vs parameter, family vs `Set`, data vs Prop, existential shape, finiteness form, naming, placement), recording the honest flip side of each choice; then stress-test via scratch simulation and dogfooding (the stress-test protocol is in SKILL.md). | [references/design-axes.md](references/design-axes.md) + [SKILL.md](SKILL.md) |
| 3 — Build the API surface | Survey-driven surface checklist (constructors, projections, ext, transport, bridges, existential layer); per-statement admission tests; statement form and attributes; the driver test — prove the downstream targets with only the proposed API, and audit what no driver uses. | [references/api-surface.md](references/api-surface.md) |
| 4 — The example sheet | Build the contrasting-cases `.lean` file; the format is grounded in the learning-science literature (contrasting cases, variation theory, erroneous examples, self-explanation, expertise reversal), with the grounding and bibliography split into an on-demand evidence file. | [references/example-sheet.md](references/example-sheet.md) + [references/example-sheet-evidence.md](references/example-sheet-evidence.md) |
| 5 — PR justification | Citation order (same-subject-area precedent first, cross-area refactors, Zulip authority, performance numbers), example sheet linked as runnable evidence, minimal-PR discipline. | [SKILL.md](SKILL.md) |

House rules, all phases: every quote is source-pinned and quoted *exactly*;
truth is the floor, not a slider (a technically-true-but-misleading form is
rejected); demonstrations must be honest (never overstate a failure); the
example sheet compiles with exactly the intended deliberate errors and zero
warnings.

## Repository layout

Each reference file is loaded when its phase begins, not up front; the
evidence file behind the example-sheet format loads only if the format
itself is questioned or amended.

```
SKILL.md                      entry point: phases, outputs, hard rules
references/
  precedent-excavation.md     the excavation playbook: source survey, git
                              archaeology, PR trail, Zulip spectator API,
                              direction of travel, holdout cost accounting,
                              verification discipline
  design-axes.md              the per-axis definition checklist, each with
                              the rule, the consumer-driven argument, the
                              precedent, and the honest flip side
  api-surface.md              the API-surface checklist: what shape-mates
                              ship, per-statement admission tests,
                              statement form, the driver test, and the
                              lighter API scratch-sheet genre
  example-sheet.md            the example-sheet recipe and the compile and
                              citation contracts
  example-sheet-evidence.md   on-demand: the learning-science grounding and
                              verified bibliography behind the sheet format
```

## Companion skill

The adaptive-teaching layer that used to live here — the learner loop that
logs every clarifying question, the eight-rule evidence-based answer-style
protocol, and the persistent gitignored learner model — was refactored out
(2026-08-01) into its own general-purpose skill, **adaptive-teacher**,
which teaches any topic, not just mathlib design. Install it alongside this
skill: when a user asks a clarifying or follow-up question about a design
explanation, this skill hands the answering to it.

## Installation

As a personal skill (available in all projects):

```bash
git clone https://github.com/homeowmorphism/mathlib-api.git ~/.claude/skills/mathlib-api
```

(The repository was renamed on GitHub 2026-08-02; the old
`mathlib-definition` URL redirects.)

As a project skill, clone into `.claude/skills/mathlib-api` inside the
project instead. Claude Code discovers the skill from the `SKILL.md`
frontmatter; invoke it explicitly with `/mathlib-api`, or just ask
Claude to design a definition or an API for mathlib / "check the
precedents" and let it trigger from the description.

## What a session needs

The skill drives ordinary tools; expect Claude to use:

- a **full-history checkout of mathlib4** — the archaeology relies on
  `git log --follow`, pickaxe (`git log -S`), and `git show <hash>^:<path>`,
  so a shallow clone will not do. The excavation commands run from the
  mathlib4 repo root; if your definition lives in a downstream project, keep
  a separate full mathlib clone around for that phase;
- the **`gh` CLI**, authenticated, for the PR trail
  (`gh pr view <n> --json title,body,comments,reviews`);
- **network access to `leanprover.zulipchat.com`** — Phase 1 reads Zulip
  through the spectator JSON API, and web search is no substitute (it does
  not index recent Zulip content);
- a **Lean 4 toolchain** to elaborate scratch simulations and compile the
  example sheet;
- optionally, whatever Mathlib search and LSP tooling you already have
  (Loogle or LeanSearch skills, live goal/diagnostic feedback) — useful
  companions, though the skill's own playbook doesn't depend on them.

## Provenance

The workflow was distilled from the `Group.Generators` /
`Group.Presentation` design project (2026-07). Two exemplar artifacts from
that project model Phases 3 and 4: `scratch_field_vs_parameter.lean`, a
contrasting-cases sheet on a definition axis (the full genre, ~690 lines
at the rename), and `scratch_closure_vs_lift.lean`, a lighter sheet on an
API axis — which Prop witnesses the generating condition, and which
statements ship around it. Case-study evidence is threaded through the reference files with
citations. The upstream ones (mathlib4 #7698, #25085, #37928, #25191; the
2021 "Bundled basis" Zulip thread) are public and checkable; the exemplar
sheets and the project-local commit hashes refer to a private working
checkout, so treat those as illustrative rather than retrievable.

## Citation

Author: Hang Lu Su ([homeowmorphism](https://github.com/homeowmorphism)).

Please cite this skill if you use it in published work, ship it inside a
tool, or build a derivative of it. GitHub's "Cite this repository" button
reads [CITATION.cff](CITATION.cff) and gives you BibTeX or APA. In plain
text:

> Su, Hang Lu. *mathlib-api: a Claude Code skill for designing Mathlib
> definitions and their API.* https://github.com/homeowmorphism/mathlib-api

## License

[Apache License 2.0](LICENSE.md): free to use, modify, and share, for any
purpose, with an explicit patent grant. Keep [LICENSE.md](LICENSE.md) and
[NOTICE](NOTICE) with any redistribution.

## Special thanks

Thank you Chris Birbeck for giving me the idea of making this workflow into a Claude skill. Thank you to everyone on the Formal Landmarks project at Imperial for inspiring me to do better as a mathlib contributor. 
