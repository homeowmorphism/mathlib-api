# Design axes — the checklist, with evidence

The recurring decision points for a mathlib data-carrying definition. For each
axis: the rule, the consumer-driven argument, the precedent, and the honest
flip side. Case-study citations refer to the `Group.Generators` /
`Group.Presentation` project (branch `Presentation`, commits
`b48a9df7117..07f73f4be5c`) and to upstream mathlib PRs.

## 1. Structure vs class

Data that a carrier can have *many of* (generating families, bases,
presentations) is a plain `structure`, not a class. Precedent in mathlib's own
words: "`PowerBasis` cannot be a class since the same algebra can have many
power bases" (`Mathlib/RingTheory/PowerBasis.lean`). The class layer, if
wanted, is a separate **Prop-valued existential** (§6). #7698 is exactly this
split: data → `FreeGroupBasis ι G`, class → Prop `IsFreeGroup`.

## 2. Index type: parameter, not field

The headline axis. The index type of the family goes in the structure's
*type* (`Group.Generators G α`), never as a `Type`-valued field. The example
sheet names the two styles in bold caps throughout — **FIELD** (index type
stored as a field) vs **PARAM** (index type as a parameter) — and this file
uses the same labels.

**Mechanism** (all demonstrated live in `scratch_field_vs_parameter.lean`):
`rw`, `simp`, and instance search match *syntax* at reducible transparency. A
**FIELD**-style construction (`(P.union Q).ι`, `(Q.comp P).vars`) is
definitionally a `⊕` but never syntactically, so:

- `rw [Sum.forall]` finds no `⊕` pattern; the repair rewrite dies with
  "motive is not type correct" because the binder type infects every later
  occurrence (Test 1);
- `simp` lemmas keyed on `Sum.elim` never fire through the projection
  (Test 2);
- propositional rewriting `rw [h]` with `h : R = P.union Q` is ill-typed for
  the same motive reason (Test 3);
- two families' `val`s cannot even be *compared* without `cast`/`HEq`
  (Test 4; compare the auto-generated `mk.injEq`: `HEq` for **FIELD**,
  honest `=` for **PARAM**);
- `Finite (P.union Q).ι` is not found by instance search though both halves
  are in scope (Test 5);
- the structure jumps a universe (`Type → Type 1`) — what `@[nolint
  checkUnivs]` suppressed on old `Algebra.Presentation` and `Module.Relations`
  still suppresses.

**Precedent**: three **FIELD**→**PARAM** refactors, never the reverse (#7698,
#25085, #37928; details and quotes in `precedent-excavation.md` §5); the
"Bundled basis" Zulip thread (Carneiro, Wieser, Kudryashov, 2021); the TODO at
`Mathlib/RingTheory/Extension/Generators.lean:41-48` reads as a field report
of the five tests. Boundary: #25191 shows *carrier* types stay bundled.

**Honest flip side**: parameters are worse at two things. (a) Plain
existentials — "has a finite generating family" has no single type to
quantify over; pay one `Finite.exists_equiv_fin` shuffle for the
`∃ n, Nonempty (… (Fin n))` bridge (§6). (b) Bundled index fields let
ext-lemmas apply without guessing the index (J. Breitner, Zulip "universe
levels for is_free_group", 2022-04-19: with unbundled generators "the
ext-lemma doesn't apply automatically … the lemma search wouldn't know which
`X`s to use"). Neither cost recurs per-proof; the **FIELD**-style costs do.

## 3. Which Prop witnesses the defining property

When several equivalent Props could be the field, pick the one that is
**elementary and self-contained** in the ambient theory, and derive the rest.
Case study: `closure_eq_top : Subgroup.closure (Set.range val) = ⊤` beat
`lift_surjective : Function.Surjective (FreeGroup.lift val)` as the field —
it mentions only subgroups of `G` (never `FreeGroup`) and matches the
`Subgroup.closure … = ⊤` idiom of `Group.FG`. The other form survives as a
derived lemma plus a smart constructor (`ofLiftSurjective`) for callers
arriving from the other direction (commit `9dc97a82a0c`).

## 4. Data vs Prop fields

"Data is expensive, Prop is free." Adding data (e.g. a section
`σ' : G → FreeGroup α` alongside surjectivity, as `Algebra.Generators` does)
buys computability — `presentedGroupEquiv` stops being `noncomputable`
because a Prop `Function.Surjective` is erased while a section is data — but
costs `ext`-ability on `val` and makes terms genuinely unequal when their
sections differ. Case-study resolution (Jul 22–23): keep the core structure
Prop-only; spin the data off into a *separate* structure (`NormalForm`)
**indexed by the term** (`s : P.toGenerators.NormalForm`), not `extends` —
extending duplicates the generators and forces an ugly matching hypothesis.
Simulate both factorings live before choosing; don't argue from taste.

## 5. Indexed family vs Set: the verb decides the container

Represent each component in the form its consuming API demands — and
different fields of one structure may demand *opposite* forms:

- Generators: you take `FreeGroup` **of** the generators; `FreeGroup α` and
  `FreeGroup.lift : (α → β) ≃ (FreeGroup α →* β)` want a *type* and a
  *family* `val : α → G`. A `Set G` here forces `α := ↥S`,
  `val := Subtype.val`, and subtype-coercion pain downstream (this is
  mathlib4 #36996's documented failure mode, cited by a reviewer in the case
  study). Families also allow repetition and canonical `Fin n` indexing.
- Relators: you take `normalClosure` **of** the relators, and
  `PresentedGroup` consumes `rels : Set (FreeGroup α)`; so
  `rel : Set (FreeGroup α)`, not `rel : ρ → FreeGroup α`. The case study
  started with the indexed family and a derived `relSet := Set.range rel`;
  every ρ-side declaration existed only to convert to the set the consumers
  wanted, and commit `6eb8cdb866d` deleted the lot (net −13 lines).

Beware the symmetry fallacy: "generators are a family, so relators should be
too" points the wrong way — the two fields have different consumers.

## 6. Existential bridges (the Prop/FG layer)

Shapes, best to worst for the finite case:

- `∃ n : ℕ, Nonempty (Generators G (Fin n))` — canonical finite index; the
  `Algebra.FinitePresentation` normal form. Adopted in commit `a9c9b3c2ade`
  (generators) and `07f73f4be5c` (presentations:
  `∃ (n : ℕ) (P : Presentation G (Fin n)), P.rel.Finite`).
- `∃ ι : Type u, …` universe-pinned — fine for the non-finite Prop class
  (`IsFreeGroup`, `IsCoxeterGroup`, `Module.Free`).
- `∃ (S : Set G) (_ : S.Finite), Nonempty (Generators G S)` — **rejected as
  misleading**: `S` enters only as an index type; the arbitrary
  `val : ↥S → G` need not be the inclusion, so the reader is invited to a
  false intuition. Reviewer: "There is no point having S : Set G here,
  because the map val … is arbitrary as opposed to the inclusion. You may as
  well use Fin n." Pinning `val = Subtype.val` instead collapses the
  structure to `Subgroup.closure S = ⊤`, i.e. restates `Group.FG`.

Truth is the floor, not a slider: all three are *true*; choose the one whose
type tells the reader the truth without the proof.

## 7. Finiteness: instance vs hypothesis

- Index **parameter** → instance argument `[Finite α]`: synthesizable,
  composes (`Finite (ι ⊕ κ)` fires). Delete any bespoke `IsFinite` class
  (#25085 did; #37928 deleted `FinEncoding` for `[Fintype Γ]`).
- **Set-valued field** → explicit hypothesis `(hrel : P.rel.Finite)`, not
  `[Finite ↥P.rel]`: an instance on a *specific* set is almost never
  synthesizable; convert once inside the proof via `hrel.to_subtype`
  (commit `07f73f4be5c`). `Set.Finite s` is defeq to `Finite ↥s` but is not a
  class, and instance search is head-symbol-directed — defeq doesn't help.

## 8. Naming, docstrings, and surface conventions

- The family field is `val` (matches `Algebra.Generators.val`, `Subtype.val`,
  `Units.val`); `toFun` is reserved for FunLike morphisms.
- No FunLike on generator-like structures: the coercion does not descend
  through `extends`, and Coxeter removed theirs — "it is unintuitive to think
  of a Coxeter system as a function" (#11836).
- `theorem` for headline results with docstrings; `lemma` for docstring-less
  helpers (the docBlame linter requires docstrings on `def`s, skips
  theorems/lemmas).
- State results in terms of the index type, not the image set, following
  #11836: "we try to state our results in terms of the type `B` that indexes
  the simple reflections, rather than the set $S$."

## 9. Placement and upstreaming

- File placement by precedent pair (e.g. `Generators.lean` beside
  `Presentation.lean`, mirroring `RingTheory/Extension/`); one structure per
  file once the API grows ("This needs to be its own file" — the case study
  split `Group.Generators` out on Jul 30).
- A bespoke-looking lemma in your file usually means the general lemma is
  missing from mathlib: extract the general statement to its natural home
  file, leave a thin corollary, and stage it as a separate prerequisite PR.
  Before adding, audit for API bloat — the case study collapsed a planned
  8-declaration chain to *one* upstream lemma after a usage count showed the
  rest were single-use bridges.
- Subject-area constraints are enforceable: the DirectoryDependency linter
  forbids `Mathlib.GroupTheory` importing geometry/topology, so
  geometric-group-theory consumers live under `Mathlib/Geometry/Group/`.
