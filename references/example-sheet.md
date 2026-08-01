# The example sheet — recipe and contracts

The dossier's argument, compiled. One `.lean` file a reader can open in the
IDE and *watch* the designs succeed and fail, plus a companion markdown
references file. Exemplar: `scratch_field_vs_parameter.lean` with its
dossier `scratch_field_vs_parameter_refs.md` (`Group.Presentation` project
root); a second, smaller instance of the genre is
`Mathlib/RingTheory/Extension/scratch.lean` there (checked `Option ℕ` vs
truncated `ℕ` subtraction — junk values made visible with paired `#eval`s).

Every element of the format instantiates a replicated learning-science
finding (contrasting cases, analogical encoding, variation theory,
erroneous examples, self-explanation, signaling, expertise reversal,
productive failure). The mapping and the verified bibliography are in
`example-sheet-evidence.md` — load that file only when the format itself
is questioned or amended, not to build a sheet.

## The recipe

1. **Distill minimal twins.** Strip the real structures to the fields that
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
2. **One shared construction** whose index type is interesting (`union` with
   `⊕`, mirroring the real `Algebra.Generators.comp`). In the losing style
   the interesting type sits in the *body*; in the winning style, in the
   *signature*. Then the equation lemmas — including the ones the losing
   style *cannot safely tag* `@[simp]` (and say why: the "repair" costs one
   hand-written cross-type lemma per projection per construction), and the
   one the winning style *doesn't need* (its job done by the typechecker;
   prove it by showing the example elaborates).
3. **Numbered tests as questions, chunked by style.** Each test header
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
4. **Escape hatches, inline and honest.** Directly under each failure, the
   ways around it — each labeled with its price ("every fix hard-codes the
   body of `union` into the proof: the definition's abstraction boundary is
   gone"). An escape hatch must recover the tool under test; a workaround
   that sidesteps the tool (e.g. `rintro` instead of `Sum.forall`) stays only
   as a labeled contrast case. If a rejected design's proof *works*, say so
   and rewrite the test to show the representative pain instead — never
   overstate.
5. **Asymmetry probes.** The sharpest lines are the unstatable ones: show
   what each side literally cannot express (`#check @GensF.mk.injEq` forced
   into `HEq` vs honest `=`; the PARAM side has no `.ι` to even state
   `union_ι` about; the universe bump via `#check (GensF : Type → Type 1)`).
6. **"The one thing the losing side does better."** Mandatory section. For
   field-style: plain existentials — and show the bridge that pays the cost
   once (`FGField ↔ FGParam` via `Finite.exists_equiv_fin`). A sheet without
   this section is advocacy.
7. **The historical reconstruction.** A `namespace OldAPI` rebuilding the
   real pre-refactor structure at demo scale from pinned history
   (`git show <hash>^:<path>`, quoted verbatim, elisions marked), the real
   construction (`comp`), and the tests reprised against it — failing on cue
   with their historical names. Include the evidence that the old API *knew*:
   its own guarded attributes (`@[simps val, simps -isSimp vars σ]` — the
   repair lemma generated but kept out of the default simp set) and the
   deleted workaround lines from the refactor's diff, per file.
8. **The live re-run.** The same tests against the current post-refactor
   mathlib API and against your new definition, succeeding.
9. **References dossier as a companion `.md` file** (`<sheet>_refs.md`, same
   directory), not a doc-comment in the sheet: inside `/-! -/` a citation
   database gets no rendering — no clickable links, no headers — and as
   markdown it distills directly into the PR description. Organized to
   mirror the argument (refactor trail → winning-style structures →
   existential layer → holdouts with their costs → related predicates),
   every entry with `path:line`, commit hash, PR URL; quotes verbatim and
   pre-verified. The sheet ends with a short `/-! ## References -/` section
   pointing at the file and saying why it is markdown; the `.md` opens by
   pointing back at the sheet.

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
  emphasis). That skill is the canonical home of the rule set and its
  evidence; on this sheet, the caps labels and the bold separators are
  the only sanctioned emphasis cues.
