# The example sheet — recipe and contracts

The dossier's argument, compiled. One self-contained `.lean` file a reader
can open in the IDE and *watch* the designs succeed and fail, opening with
the real-Mathlib Context section and ending with the references dossier as
a `/-! ## References -/` section. Exemplar:
`scratch_field_vs_parameter.lean` (`Group.Presentation` project root); a
second, smaller instance of the genre is
`Mathlib/RingTheory/Extension/scratch.lean` there (checked `Option ℕ` vs
truncated `ℕ` subtraction — junk values made visible with paired `#eval`s).
For a contested API choice there is a lighter variant of this genre — no
historical reconstruction, local `path:line` pins only — specified in
`api-surface.md` §5; its exemplar is `scratch_closure_vs_lift.lean`.

Every element of the format instantiates a replicated learning-science
finding (contrasting cases, analogical encoding, variation theory,
erroneous examples, self-explanation, signaling, expertise reversal,
productive failure), with one recorded exception: the Context section
sits first by user decision, not by the literature. The mapping, the
verified bibliography, and that override note are in
`example-sheet-evidence.md` — load that file only when the format itself
is questioned or amended, not to build a sheet.

## The recipe

1. **The Context section opens the sheet.** Immediately after the module
   doc, a `/-! ## Context: the real thing -/` section shows what the toys
   distill: the real structure(s) quoted verbatim from pinned history
   (`git show <hash>^:<path>`, elisions marked), the review exchange where
   the choice was debated (PR quotes, verbatim), the disputed field's real
   consumers, and the evidence that the old API *knew* its costs — guarded
   attributes (`@[simps val, simps -isSimp vars σ]` — the repair lemma
   generated but kept out of the default simp set) and the refactor diff's
   deleted workaround lines, per file. Prose may point forward to the
   tests ("Test 1 below"); anything executable against them (the OldAPI
   reprise, the live re-run) stays after the tests it re-runs. Context
   goes first by standing user decision (question-log 2026-08-02, "Just
   put the context first … I like it first"), overriding the earlier
   cases-first placement; the evidence file's first bullet records the
   override. Both full-genre exemplars were migrated to this order on
   2026-08-02; the lighter-genre exemplar has no Context section.
2. **Distill minimal twins.** Strip the real structures to the fields that
   participate in the phenomenon (two fields sufficed for
   `GensF`/`GensP`; `σ'` and the algebra bookkeeping were elided *and the
   elision stated*). Name each twin with a pronounceable suffix
   abbreviating its style (`GensClos`/`GensLift`), never a bare initial
   (`GensC`/`GensL`): single-letter suffixes are hard to keep apart while
   reading (user feedback, question-log 2026-08-02). Helper defs follow
   suit (`toClos`/`toLift`, not `toC`/`toL`). The exemplar's
   `GensF`/`GensP` predates this rule. Both twins defined adjacently, each
   with a docstring opening with its style's caps label (`FIELD:` /
   `PARAM:` — plain, since bold is reserved for the separators), because
   hover shows docstrings out of context.
3. **One shared construction** whose index type is interesting (`union` with
   `⊕`, mirroring the real `Algebra.Generators.comp`). In the losing style
   the interesting type sits in the *body*; in the winning style, in the
   *signature*. Then the equation lemmas — including the ones the losing
   style *cannot safely tag* `@[simp]` (and say why: the "repair" costs one
   hand-written cross-type lemma per projection per construction), and the
   one the winning style *doesn't need* (its job done by the typechecker;
   prove it by showing the example elaborates).
4. **Numbered tests as questions, chunked by style.** Each test header
   is a question ("can `simp` see `Sum.elim` through `.val`?"); inside it,
   bold separator blocks — `/-! **FIELD** -/` → `/-! **FIELD** escape
   hatches -/` (where any exist) → `/-! **PARAM** -/` — chunk the material
   by style. Plain bold, not `###`: a dozen identical "FIELD"/"PARAM"
   outline entries would be useless as navigation, so reserve real `##`/`###`
   headers for unique titles and let the separators work in the text itself.
   The caps names are introduced in the module doc and bolded *only* in the
   separators — everywhere else (prose, docstrings, headers) the caps name
   alone is the label, and never bold inside code, identifiers, or verbatim
   quotes. Each example carries its `Says:` line stating the mathematical
   claim in plain English (flag deliberately trivial claims: "the content
   is not the claim but its shape"); each deliberate failure is labeled
   `EXPECTED ERROR: <verbatim compiler text>`.
5. **Escape hatches, inline and honest.** Directly under each failure, the
   ways around it — each labeled with its price ("every fix hard-codes the
   body of `union` into the proof: the definition's abstraction boundary is
   gone"). An escape hatch must recover the tool under test; a workaround
   that sidesteps the tool (e.g. `rintro` instead of `Sum.forall`) stays only
   as a labeled contrast case. If a rejected design's proof *works*, say so
   and rewrite the test to show the representative pain instead — never
   overstate.
6. **Asymmetry probes.** The sharpest lines are the unstatable ones: show
   what each side literally cannot express (`#check @GensF.mk.injEq` forced
   into `HEq` vs honest `=`; the PARAM side has no `.ι` to even state
   `union_ι` about; the universe bump via `#check (GensF : Type → Type 1)`).
7. **"The one thing the losing side does better."** Mandatory section. For
   field-style: plain existentials — and show the bridge that pays the cost
   once (`FGField ↔ FGParam` via `Finite.exists_equiv_fin`). A sheet without
   this section is advocacy.
8. **The historical reconstruction.** A `namespace OldAPI` rebuilding at
   demo scale the pre-refactor structure the opening Context section
   already quotes (cite back, don't re-quote), the real construction
   (`comp`), and the tests reprised against it — failing on cue with
   their historical names.
9. **The live re-run.** The same tests against the current post-refactor
   mathlib API and against your new definition, succeeding.
10. **References dossier as the sheet's closing section**: a
   `/-! ## References -/` block at the end of the file, not a separate
   `.md`. A companion `<sheet>_refs.md` was tried and reverted: rendered
   links did not pay for the second file a reader must keep alongside the
   sheet (user decision, 2026-08-02). Organized to mirror the argument
   (refactor trail → winning-style structures → existential layer →
   holdouts with their costs → related predicates), every entry with
   `path:line`, commit hash, PR URL; quotes verbatim and pre-verified.
   Markdown link syntax stays; it reads fine in source and lifts wholesale
   into the PR description. Lean block comments nest, so the section text
   must contain no `/-` or `-/` sequences.

## Contracts

- **Compile contract**: the file compiles with *exactly* the N intended
  errors, all at `EXPECTED ERROR` examples, zero warnings. Re-run full
  diagnostics after every edit and compare the error set byte-for-byte.
  Track N explicitly (one example can emit two messages — count messages,
  not examples, and say which convention you use).
- **Citation contract**: every quoted signature, attribute, TODO, commit
  message, and deleted line carries a `(Source: …)` note pinned to a commit
  (`git show <hash>^:<path>`, line number) or PR URL. Citing obliges quoting
  exactly — the case study caught a silently trimmed attribute
  (`vars` for `vars σ`) only because of this rule.
- **Placement**: the sheet lives in the project root so the IDE elaborates it
  against the project's mathlib; it declares itself scratch ("not part of the
  library. Delete freely."); imports are minimal and named for why they're
  needed.
- **Comment style**: all commentary in `/- ... -/` blocks; module sections
  `/-! -/`; docstrings `/-- -/` on the two structure twins and any `def`s
  (Lean forbids docstrings on anonymous `example`s — put their `Says:` lines
  in a preceding block comment). Never `--`.
- **Readability pass**: after the content is complete, do a dedicated prose
  pass, then verify the diagnostic set is unchanged. The exemplar needed
  two: a structural cut (314 → 231 lines, on "it's an overwhelming wall of
  text") and a later sentence-level rewrite (on "so LLM-y that they are
  overwhelming to read"). Section headers become questions. For the
  sentences, apply the `human-prose` skill (one idea per sentence; no
  mid-sentence asides; active voice; given before new; no decorative
  emphasis; name the referent). That skill is the canonical home of the
  rule set and its evidence; on this sheet, the caps labels and the bold
  separators are the only sanctioned emphasis cues.
- **Verdict blocks take the pass first.** A "why it matters" block states
  the conclusion a test is evidence for, and it is where register
  complaints land: "Wtf does this mean?" (question-log 2026-08-02, on a
  seven-line block that the pass had already run over). Three rules on top
  of `human-prose`. The verdict is the block's first sentence, in plain
  subject-verb-object form. Each supporting claim gets its own sentence and
  its own check — the grep command, the quoted fields, the line number in
  this file where the reader can see it. Each abstract cost noun ("price",
  "discipline", "liveable") is replaced by the artifact that realizes it.
  The contrast, verbatim from `scratch_section_vs_none.lean` before and
  after that complaint:

      before: "presentation-independence is phrased through `Hom`s
        instead, and `Hom` imposes *no* compatibility with σ … That
        discipline is what makes the bundled section liveable."
      after:  "That map is `Hom`, and it constrains only the generating
        family … σ is absent. … So the bundled σ costs nothing here: the
        bill exists, but nothing in mathlib ever sends it."
