# Precedent excavation — the playbook

How to reconstruct what mathlib tried, what it settled on, and why. Every
technique below was used in the `Group.Generators`/`Group.Presentation` case
study (2026-07); commands are the ones that actually ran.

## 1. Survey the living library

Find structures with the same *shape* as your candidate, not just the same
name. For a "chosen data attached to a carrier" structure, the shape-mates are
`Basis`-family structures, `Generators`/`Presentation` structures, and
existential Prop classes over them.

```bash
grep -rn "structure Presentation" Mathlib --include="*.lean" -l
grep -rn "structure .*Generators" Mathlib --include="*.lean" -l
```

Read each hit **fully** (fields, attributes, implementation notes, TODOs —
the TODO block of `Mathlib/RingTheory/Extension/Generators.lean:41-48` was the
single most valuable document in the case study). Then classify every
precedent into one of four buckets:

1. **Never-bundled predicate** (`AlgebraicIndependent`, `Orthonormal`) — the
   pre-2021 style, still the norm in some niches.
2. **Bundled data over a parameter index** — the **PARAM** style
   (`Module.Basis ι R M`, `AffineBasis`, `FreeGroupBasis ι G`, post-#25085
   `Algebra.Generators R S ι`, `CoxeterSystem M W`) — the modern default.
3. **Fully bundled** — the **FIELD** style (index type as a field:
   `Module.Relations`, `SheafOfModules.GeneratingSections`, `PowerBasis` with
   its `dim : ℕ`, `IsFreeGroupoid`) — the holdouts; for each, record the
   price it pays (see §6).
4. **Existential Prop layer over bucket 2** (`Module.Free`, `IsFreeGroup`,
   `IsCoxeterGroup`, `Algebra.FinitePresentation`) — universe-pinned
   `∃ ι : Type u` or `Fin n`-normalized.

When the deliverable includes an API surface, record for each shape-mate
not just how it is *declared* but what it *ships*: classify its companion
declarations into the surface categories of `api-surface.md` §1, note
attribute discipline verbatim (`@[simp]`, `@[simps]`, `-isSimp` guards),
and treat a standard category a shape-mate lacks as a finding in itself.

The case-study sweep that produced this taxonomy fanned out four parallel
searchers (linear/commutative algebra; group theory; an open grep for
`Type`-valued fields; git archaeology for bundling refactors) and then
**adversarially verified every claimed case against source** — 46 confirmed,
3 refuted. Never skip the verification pass; the refuted cases are exactly the
ones that would have embarrassed the PR.

## 2. Git archaeology

The history of a definition is a record of paid costs. Commands, in the order
they earn their keep:

```bash
git log --follow --oneline -- Mathlib/RingTheory/Extension/Generators.lean

# Pickaxe: date the pain by searching for when a TODO/docstring text appeared
git log -S "Currently, Lean does not see through" --oneline

# Quote the PRE-refactor state (the ^ is the point)
git show 966daba4dd6^:Mathlib/RingTheory/Extension/Generators.lean

# Blast radius: how many files a design change touched, and the net line count
git show --stat 966daba4dd6

# Who introduced a line and in which PR (with -C -C to survive file moves)
git blame -L 152,155 -C -C -- Mathlib/GroupTheory/FinitelyPresentedGroup.lean
```

Read the numbers as evidence: #25085's unbundling was net **−59 lines across
9 files** — the empirical sign the bundled version was costing proof effort.
Deleted lines in a refactor diff are a catalogue of the old design's daily
workarounds (in the case study: `comp_vars` supplied by hand in `simp only`
after `simp only` in `JacobiZariski.lean`, once even backwards as
`← comp_vars` in `StandardSmooth.lean` — quoted verbatim in the example
sheet's Context section).

## 3. The PR trail

```bash
gh pr view 25085 -R leanprover-community/mathlib4 --json title,body,comments,reviews
```

Harvest, verbatim:

- **Commit messages / PR bodies** — often the only written rationale. #7698:
  bundled generator data "is bad, as there are many sets of generators in a
  free group, and changing sets of generators happens all the time in
  geometric group theory."
- **Reviewer generalizations** — eric-wieser on #25085: "generally bundling
  types is a mistake outside of category theory."
- **`leanprover-bot` benchmark comments** — #25085 posted instruction-count
  improvements of −16.2% (`Extension.Generators`) to −29.4%
  (`Cotangent.LocalizationAway`). Performance deltas are admissible design
  evidence in review.
- **Cross-references to failed experiments** — chrisflav approving #25085:
  "the experiment for doing the same with `Extension` horribly failed
  (#25191)". See §5.

Note: bors-merged PRs report `mergedAt: null` on GitHub; take merge dates from
the bors commit in local master history.

## 4. Zulip

Web search does **not** index recent Zulip content and the community archive
mirror is stale. Use the spectator JSON API for web-public channels
(`leanprover.zulipchat.com/json/messages` with a `channels: web-public`
narrow); it returns exact message text with authorship.

What Zulip adds that PRs don't: the *arguments*, with names attached. The
canonical bundled-vs-parameter authority is the "Bundled basis" thread
(general, Apr 2021,
https://leanprover.zulipchat.com/#narrow/channel/113488-general/topic/Bundled.20basis):

- Eric Wieser: "If you bundle iota, then to talk about bases over the same
  index you have to start inserting proofs that the indexes are the same."
- Mario Carneiro: "it's generally not a good idea to bundle types for the
  reason Eric Wieser mentioned" — and, on `is_free_group`: "it might even be
  useful to pull the generators out of the structure too, have a function
  `f : I -> G` as a parameter."

Also mine **gap evidence** — threads showing users wanting your definition:
"Stating that a group has a specific presentation" (Is there code for X?,
Oct 2025): "there is not currently a way to package it into one statement"
(V. Tsyrklevich). A documented unmet need is the opening paragraph of your PR.

Caveats: some design discussion lives in non-public channels (the
Group.Presentation ItaLean-2025 thread is spectator-inaccessible); and absence
of a Zulip thread is itself a finding — #7698 and #25085 have no public design
thread, their rationale lives only in the PR.

## 5. Direction of travel, and failed reverse experiments

The strongest form of precedent evidence is a *fleet* of refactors all moving
one way:

- `is_basis` predicate → bundled `basis ι R M` **with `ι` a parameter** —
  mathlib3 #7496 (A. Baanen, merged 2021-05-10). (The RFC that sparked it,
  #4949, was closed unmerged — cite #7496, not #4949.)
- `IsFreeGroup` (bundled generators field) → `FreeGroupBasis ι G` + Prop class
  — mathlib4 #7698 (S. Gouëzel, 2023-10-28, commit `bab05758f84`).
- `Algebra.Generators`/`Algebra.Presentation` `vars`/`rels` fields →
  parameters `ι`/`σ`; bespoke `IsFinite` class deleted for
  `[Finite ι] [Finite σ]` — mathlib4 #25085 (A. Yang, 2025-06-02,
  commit `966daba4dd6`).
- `Computability.Encoding` `Γ` field → parameter; `FinEncoding` deleted for
  `[Fintype Γ]` — mathlib4 #37928 (2026-06-30, commit `ca158545413`).

Three **FIELD**→**PARAM** unbundlings, zero in the reverse direction. Then look
for the reverse experiment that *was* tried: #25191 attempted to unbundle the
carrier `Extension.Ring` and failed on benchmarks — which sharpens the rule to
"unbundle **index** types; keep **carrier** types bundled." The boundary of a
principle is as citable as the principle.

## 6. Cost accounting of holdouts

Trend + counterexamples-that-pay is far more convincing than trend alone. For
each surviving bundled case, name the visible price, from its own source:

- `Module.Relations` — `set_option linter.checkUnivs false` plus the
  `IsPresentationCore` universe-shrinking apparatus
  (`Mathlib/Algebra/Module/Presentation/Basic.lean`).
- `PowerBasis` — bundles `dim : ℕ`; all comparison routed through equivs;
  its own docs: "`PowerBasis` cannot be a class"
  (`Mathlib/RingTheory/PowerBasis.lean`).
- `SheafOfModules.GeneratingSections` — needs a `shrink` def; finiteness as a
  class on the structure.
- `IsFreeGroupoid` — own docstring: "This definition is nonstandard."
- Legitimate bundled use: `Algebra.Smooth.DescentAux` *locally* re-bundles the
  parameterized `Presentation` when a proof needs "some presentation" as one
  existential package — bundling as a proof-local move, not an API.

## 7. Verification discipline

- Re-fetch every PR fact before it enters a document (`gh pr view`); the case
  study carried a wrong PR number (#4949 for #7496) through two artifacts
  before a re-fetch caught it — wrong PR, wrong author, wrong merge status.
- Quote signatures and attributes **verbatim** including things that look
  trimmable (`@[simps val, simps -isSimp vars σ]`, not a shortened form).
- Pin historical quotes so they are reproducible:
  `(Source: git show 966daba4dd6^:Mathlib/RingTheory/Extension/Generators.lean, line 57)`.
- Write durable conclusions to a memory/notes file at the end of the sweep,
  with the citation trail, so the PR-justification phase doesn't re-run the
  excavation.
