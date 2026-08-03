# The API surface — which statements ship, and why

The consumer principle at statement granularity: a lemma is an interface
promise, and it enters the surface the same way a field enters the
structure — because a named consumer demands it, never for symmetry or
completeness. This checklist was distilled from a four-family survey of
the shipped surfaces of `Module.Basis`, `Algebra.Generators` /
`Algebra.Presentation`, `FreeGroupBasis`, and `CoxeterSystem` in the
case-study checkout (2026-08-02, branch `Presentation`), plus the
case-study API itself; every quoted comment below was re-verified against
source. Local `path:line` pins refer to that checkout.

## 1. The standard surface — what shape-mates ship

Six categories cover the companion declarations of every surveyed
bundled-data structure. Survey Phase 1 precedents against them; an absent
category is a finding either way (a deliberate omission with a reason, or
a real gap — `IsCoxeterGroup` is a bare existential class with no intro,
elim, or transport lemma anywhere in the checkout, and that is a gap).

1. **Constructors.** Rename the anonymous constructor to free the good
   name for the smart constructor: `Basis` declares `ofRepr ::`
   (`Mathlib/LinearAlgebra/Basis/Defs.lean:92`) so `Basis.mk` can be the
   mathematically meaningful one (from `LinearIndependent` + span,
   `Basis/Basic.lean:111`). Then one smart constructor per *arrival
   direction*: `Algebra.Generators.ofSurjective` / `ofSet` / `ofAlgHom`
   (`Mathlib/RingTheory/Extension/Generators.lean:127/162/156`),
   `FreeGroupBasis.ofLift` / `ofUniqueLift`
   (`Mathlib/GroupTheory/FreeGroup/IsFreeGroup.lean:141/160`), the case
   study's `ofLiftSurjective`. Plus canonical instances (`self`,
   `mvPolynomial`, `ofFreeGroup`).
2. **Projection and evaluation lemmas.** Every constructor ships the
   `@[simp]` lemma saying what it evaluates to (`Basis.coe_mk : ⇑(mk hli
   hsp) = v`, `Basis/Basic.lean:126`; the case study's
   `val_ofLiftSurjective`, `val_self`). A constructor without its
   evaluation lemma is unusable; an evaluation lemma without its
   constructor is unstatable — they are admitted as a pair.
3. **Ext and injectivity.** Two distinct products, easy to conflate: ext
   *for the structure* (`Basis.eq_of_apply_eq` is the sole `@[ext]`,
   comparing the coe, not the `repr` field, `Basis/Defs.lean:370`) and
   ext *for morphisms given the structure* (`Basis.ext` for semilinear
   maps, `Defs.lean:318`; `FreeGroupBasis.ext_hom`; Coxeter's
   `ext_simple`). Refusals are precedents too: `IsFreeGroup.ext_hom`
   carries the comment "Do not register this as an ext lemma, as
   `Generators G` is not canonical."
   (`Mathlib/GroupTheory/FreeGroup/IsFreeGroup.lean:214`), and
   `Algebra.Generators` ships *no* structure-ext at all — equal `val`s
   can hide unequal sections.
4. **Transport.** `reindex` along an index equiv, `map` along a carrier
   equiv or hom, `comp`/`prod`/`baseChange`. `reindex` exists *because*
   the index is a parameter (`Algebra.Generators.reindex`,
   `Extension/Generators.lean:337`, post-#25085); its evaluation lemma
   may stay non-simp when it is plain `rfl` (`reindex_val`, `:346`).
5. **Bridges** to the pre-existing predicates and idioms:
   `Basis.span_eq` / `Basis.linearIndependent`
   (`Basis/Basic.lean:72/86`), `CoxeterSystem.subgroup_closure_range_simple`
   (`Mathlib/GroupTheory/Coxeter/Basic.lean:241`), the case study's
   `Generators.fg : … → Group.FG G`. Highest value per line: bridges are
   what make the new structure reachable from what users already hold.
6. **The existential layer.** Mint a new Prop class only when no
   predicate exists (`Module.Free`, sigma-packed,
   `Mathlib/LinearAlgebra/FreeModule/Basic.lean:43`); otherwise pair
   with the existing predicate via a `Fin n`-normalized iff
   (`Algebra.FiniteType.iff_exists_generators : FiniteType R S ↔ ∃ n,
   Nonempty (Generators R S (Fin n))`, `Extension/Generators.lean:381`;
   the case study's `Group.fg_iff_nonempty_finite_generators`). Add
   choice extractors only when consumers need a witness by name
   (`Module.Free.chooseBasis`; the `ofFinitePresentation` trio,
   `Extension/Presentation/Basic.lean:142-154` — two ℕ-defs must precede
   the data def, a real cost of parameter indices, priced in
   `design-axes.md` §2's flip side).

## 2. Admission tests — when a statement earns its slot

Each candidate must pass at least one test, and the dossier records
which:

1. **A named consumer.** A proof site, argument slot, or documented gap
   (Zulip "is there code for X") written down next to the statement.
   The audit that enforces it is a usage count: the case study collapsed
   a planned 8-declaration upstream chain to one lemma after the count
   showed the rest were single-use bridges (`design-axes.md` §9), and
   commit `6eb8cdb866d` deleted a parallel ρ-side bridge ladder for
   net −13 lines.
2. **A precedent-standard slot.** Every surveyed shape-mate ships it, so
   its absence would read as a gap: each constructor's evaluation lemma,
   `reindex`/`map`, the morphism-ext given the structure. This test
   legitimizes lemmas with no consumer *yet* — the case study's
   `presentedGroupEquiv_of` had zero checkout consumers but fills the
   slot `Basis.coe_mk` fills. Flag such admissions explicitly.
3. **A conversion moved out of every use site.** A bridge belongs when it
   replaces a rewrite the user would otherwise write, and only when that
   rewrite is more than one application of an existing iff. The case
   study keeps `Group.Generators.lift_surjective`
   (`Mathlib/GroupTheory/Generators.lean:70`) because it is one line with
   a consumer eleven lines below it, and ships no constructor in the
   reverse direction, because
   `⟨f, FreeGroup.closure_range_eq_top_iff_surjective_lift.mp h⟩` already
   is the conversion.

   An earlier version of this test read "51 closure-shaped theorems
   against 0 lift-shaped" and quoted the sheet concluding that a
   conversion paid once beats the same conversion paid forever. The 0 was
   a regex artifact (`example-sheet.md`, honesty contract), and neither
   design pays more than one application. What those counts bear on is
   which statement to make the *field* (`design-axes.md` §3), not which
   bridges to ship.

Standing rejections:

- **One-step restatements** a caller can compose at the use site — unless
  the restatement is the simp-normal form or removes a `cast`/`HEq`.
  Directional corollaries of a canonical iff are admitted when each
  direction has its own arrival direction: the case study's
  `lift_surjective` and `ofLiftSurjective` are literally `.mp` and
  `.mpr` of `FreeGroup.closure_range_eq_top_iff_surjective_lift` applied
  to the field, and both survived because constructors and consumers
  arrive from opposite sides.
- **Courtesy restatements without a label.** `IsFreeGroup.unique_lift`
  ships the ∃!-form but its docstring says why the Equiv is primary:
  "Note that since `IsFreeGroup.lift` is expressed as a bijection, it
  already expresses the universal property"
  (`IsFreeGroup.lean:218-222`). A courtesy form is fine; an unlabeled
  second normal form is a trap.
- **Scaffolding in the surface.** Keep construction internals `private`
  (Coxeter's lift machinery exposes only the bundled Equiv and one simp
  lemma, `Coxeter/Basic.lean:298-352`; the `Presentation.comp` aux suite
  is private, `Extension/Presentation/Basic.lean:346-413`). The public
  surface is the contract; everything public will be depended on.

## 3. Statement form and attributes

- **Index type over image set.** State results in terms of the indexing
  type, not the subset it carries onto — Coxeter's implementation note:
  "we state our results in terms of $B$ wherever possible"
  (`Coxeter/Basic.lean:44-50`); same rule as `design-axes.md` §8.
- **One simp-normal form, and orientation toward it.** Point simp lemmas
  at the pre-existing idiom (`Basis.repr_symm_apply` normalizes the
  field's inverse to `Finsupp.linearCombination`, `Basis/Defs.lean:139`).
  Do not tag what simp already proves: `range_reindex` is deliberately
  unsimped with the comment "`simp` can prove this as `Basis.coe_reindex`
  + `EquivLike.range_comp`" (`Basis/Defs.lean:209`). Generate but
  withhold projection lemmas for fields that must not leak into simp:
  `@[simps val, simps -isSimp σ]` on `Algebra.Generators.localizationAway`
  (`Extension/Generators.lean:206`) and `comp`; redirect generated names
  off raw fields via `initialize_simps_projections (σ' → σ)`
  (`Extension/Generators.lean:95`).
- **Instance placement.** A bridge whose structure argument cannot be
  synthesized is a lemma, not an instance: "Can't be an instance because
  the basis can't be inferred." (`Basis/Basic.lean:291`, on
  `Basis.isTorsionFree`); the instance belongs on the existential class
  (`Module.Free` → `IsTorsionFree` *is* an instance,
  `FreeModule/Basic.lean:103`). Finiteness form follows
  `design-axes.md` §7.
- **File placement.** The definition file sits upstream of what it
  bridges to, enforced by `assert_not_exists LinearMap.pi
  LinearIndependent Cardinal` (`Basis/Defs.lean:63`); bridges live one
  file downstream, the existential class in its own directory. General
  lemmas upstream to their home file per `design-axes.md` §9.
- **Docs die with their API.** #25085 deleted
  `Algebra.Presentation.IsFinite` but the module doc still advertises it
  (`Extension/Presentation/Basic.lean:27`) — a live reminder that every
  deletion audit includes docstrings and module docs.

## 4. The driver test

The empirical check, run in scratch before the PR. Write the downstream
target proofs — the theorems the definition exists to enable — using only
the proposed surface.

- **Friction names a missing lemma.** Every spot where the driver proof
  reaches around the API (unfolds a definition, re-proves a conversion,
  inserts a `cast`) is an admission case with the consumer already
  written down. This is Phase 2's dogfooding, now aimed at the lemma
  list instead of the fields.
- **Silence names a bloat candidate.** After the drivers compile, count
  consumers per lemma. Zero-consumer lemmas survive only by admission
  test 2 (precedent-standard slot) or 3 (open-ended construction sites),
  and the dossier must say which. The 2026-08-02 survey of the
  case-study branch found 8 of its 24 declarations with no checkout
  consumer yet — each needs that justification recorded, or a deletion
  ticket.
- Weight the two site kinds asymmetrically, as in admission test 3: a
  lemma for a closed consumption list must show its consumers now; a
  constructor-side lemma may cite the open-ended supply count instead.

## 5. The API scratch sheet — the lighter genre

When an API choice is contested (which Prop is the field, which form is
simp-normal, iff vs corollaries), demonstrate it. The full
`example-sheet.md` genre usually over-serves here; the exemplar of the
lighter genre is `scratch_closure_vs_lift.lean` (case-study project
root). Deltas from the full recipe:

- **No historical reconstruction** when mathlib never flipped the axis —
  the sheet says so and argues from shape: "Mathlib has never flipped
  this particular field, so unlike `scratch_field_vs_parameter.lean`
  there is no historical refactor to reconstruct; the precedent is
  shape-level" (its References section).
- **Local pins suffice.** With no refactor history, the closing
  `/-! ## References -/` section carries plain `path:line` pins and grep
  counts instead of commit-pinned `git show` quotes. Report each count as
  the command and its output, and run the pattern that would find the
  counterexamples too. The exemplar originally reported its supply as
  "51 to 0". That was an artifact of a regex that cannot match mathlib's
  `∃ (φ : FreeGroup α →* G), Function.Surjective φ` statements, and
  mathlib has two of them.
- **Everything else carries over** from `example-sheet.md`: twins with
  pronounceable style suffixes, numbered tests phrased as questions,
  `Says:` lines, labeled `EXPECTED ERROR`s with verbatim compiler text,
  escape hatches priced honestly, the mandatory "one thing the losing
  side does better" section, the limitations section, the honesty,
  compile and citation contracts, and the readability pass.

Escalate back to the full genre when the axis *does* have a refactor
history — then the historical reconstruction and the companion references
`.md` earn their cost.
