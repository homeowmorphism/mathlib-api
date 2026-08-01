# The example sheet — format and evidence base

The dossier's argument, compiled. One `.lean` file a reader can open in the
IDE and *watch* the designs succeed and fail. Exemplar:
`scratch_field_vs_parameter.lean` (575 lines, `Group.Presentation` project
root); a second, smaller instance of the genre is
`Mathlib/RingTheory/Extension/scratch.lean` there (checked `Option ℕ` vs
truncated `ℕ` subtraction — junk values made visible with paired `#eval`s).

## Why this format — the learning-science grounding

The sheet is built to convince two audiences at once: a reader new to the
design question, and an expert reviewer. Each format element below is the
instantiation of a replicated finding (full citations in §Bibliography):

- **Contrasting cases, before the prose.** Analyzing contrasting cases
  prepares learners to understand a subsequent explanation far better than
  reading the explanation first (Schwartz & Bransford 1998). Hence: the twin
  structures and failing/succeeding examples come first; the prose thesis
  ("PARAM's cost is in signatures, read once; FIELD's cost is in proofs,
  fought continuously") is stated only after the reader has watched the
  failures.
- **Side-by-side pairs, not sequential sections.** Explicitly comparing two
  cases induces a transferable schema; studying the same cases separately
  does not (Gentner, Loewenstein & Thompson 2003; Gick & Holyoak 1983 — one
  analog rarely transfers, two with comparison do). Hence: each **FIELD**
  example is immediately followed by its **PARAM** twin, and the sheet cites
  at least two independent historical refactors, never one.
- **Bold style labels as visual signals — one signal per scope.** Give the
  contrasted styles short caps names, introduce them in the module doc, and
  set them in bold where they carry structure: separator blocks
  (`/-! **FIELD** -/`), the twin structures' docstrings (docstrings show
  on hover out of context, so they stay self-contained), and flowing prose
  that names a style mid-sentence. Cueing the relevant dimension reliably
  improves retention and transfer (signaling principle: Schneider et al.
  2018 meta-analysis) — but do NOT echo the label at the top of every
  example under an already-labeled separator: a repeated cue is noise, and
  the separator already tells the reader which side of the contrast
  they are on. Never bold inside code, identifiers, or verbatim quotes.
- **Vary one thing; hold everything else invariant.** A critical aspect is
  discernible only when it varies against an invariant background (variation
  theory: Marton & Pang 2006). Hence: `GensF`/`GensP` differ in *exactly* one
  choice (where `ι` lives); same field names, same `union` construction, same
  test propositions.
- **Deliberate errors, explicitly flagged.** Studying incorrect examples
  fosters transfer, but low-prior-knowledge learners benefit only when the
  errors are *highlighted* (Große & Renkl 2007; Adams et al. 2014). Hence
  the `EXPECTED ERROR:` label carrying the verbatim compiler message — the
  error is the payload, and no reader should wonder whether the file is
  broken.
- **Self-explanation prompts.** Learners who explain to themselves what each
  worked step means learn more (Chi et al. 1989; Renkl 2014: examples work
  when actively self-explained). Hence the `Says:` docstring discipline —
  every example states its mathematical claim in plain English, and flags
  when the claim is deliberately trivial ("the content is not the claim but
  its shape").
- **Layering for the expertise-reversal effect.** Guidance that helps novices
  becomes redundant, even harmful, for experts (Kalyuga et al. 2003). Hence
  the sheet layers: tests + escape hatches for the newcomer; the historical
  reconstruction and the terse, citation-dense References section for the
  reviewer who wants only the trail.
- **Struggle before resolution.** Wrestling with the failing cases before the
  canonical solution can improve learning from it (Kapur 2008 — with the
  caveat that the 2008 comparison was against well-structured problem
  solving; the struggle-then-instruction design is Kapur 2012). Hence
  failures precede fixes, and escape hatches precede the design that makes
  them unnecessary.

Folklore corrections worth knowing when citing these: Sweller & Cooper 1985
showed the worked-example effect on *similar* problems, not far transfer;
Renkl's canonical review is in *Cognitive Science* (2014), often miscited to
an education journal.

## The recipe

1. **Distill minimal twins.** Strip the real structures to the fields that
   participate in the phenomenon (two fields sufficed for
   `GensF`/`GensP`; `σ'` and the algebra bookkeeping were elided *and the
   elision stated*). Both twins defined adjacently, each with a docstring
   naming its style in the bold caps label form (`**FIELD**:` /
   `**PARAM**:`) — the docstring is the one place the label is repeated,
   because hover shows it out of context.
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
   Each example carries its `Says:` line; each deliberate failure is labeled
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
9. **References section** at the end, one doc-comment, organized to mirror
   the argument (refactor trail → winning-style structures → existential
   layer → holdouts with their costs → related predicates), every entry with
   `path:line`, commit hash, PR URL; quotes verbatim and pre-verified.

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
- **Readability pass**: after the content is complete, do a dedicated
  simplification pass (the exemplar went 314 → 231 lines on the instruction
  "it's an overwhelming wall of text"); section headers become questions;
  verify the diagnostic set is unchanged afterwards.

## Bibliography (verified 2026-07-31; one-line findings faithful to the papers)

- Schwartz, D. L., & Bransford, J. D. (1998). A time for telling.
  *Cognition and Instruction*, 16(4), 475–522. doi:10.1207/s1532690xci1604_4.
  Analyzing contrasting cases prepared students to learn from a subsequent
  lecture that others could not exploit.
- Sweller, J., & Cooper, G. A. (1985). The use of worked examples as a
  substitute for problem solving in learning algebra. *Cognition and
  Instruction*, 2(1), 59–89. doi:10.1207/s1532690xci0201_3. Studying worked
  examples beat problem solving on similar problems (no far transfer shown).
- Chi, M. T. H., Bassok, M., Lewis, M. W., Reimann, P., & Glaser, R. (1989).
  Self-explanations: How students study and use examples in learning to
  solve problems. *Cognitive Science*, 13(2), 145–182.
  doi:10.1016/0364-0213(89)90002-5. Successful learners spontaneously
  self-explain worked examples; unsuccessful ones copy them.
- Gick, M. L., & Holyoak, K. J. (1983). Schema induction and analogical
  transfer. *Cognitive Psychology*, 15(1), 1–38.
  doi:10.1016/0010-0285(83)90002-6. One prior analog rarely transfers; two,
  with comparison, induce a usable schema.
- Gentner, D., Loewenstein, J., & Thompson, L. (2003). Learning and
  transfer: A general role for analogical encoding. *Journal of Educational
  Psychology*, 95(2), 393–408. doi:10.1037/0022-0663.95.2.393. Comparing two
  cases side by side produced transfer that studying them separately did not.
- Kalyuga, S., Ayres, P., Chandler, P., & Sweller, J. (2003). The expertise
  reversal effect. *Educational Psychologist*, 38(1), 23–31.
  doi:10.1207/S15326985EP3801_4. Supports that help novices become redundant
  or harmful for experts.
- Marton, F., & Pang, M. F. (2006). On some necessary conditions of
  learning. *Journal of the Learning Sciences*, 15(2), 193–220.
  doi:10.1207/s15327809jls1502_2. An aspect is learnable only when varied
  against an invariant background.
- Große, C. S., & Renkl, A. (2007). Finding and fixing errors in worked
  examples: Can this foster learning outcomes? *Learning and Instruction*,
  17(6), 612–634. doi:10.1016/j.learninstruc.2007.09.008. Incorrect examples
  help — for low prior knowledge, only when the errors are highlighted.
- Kapur, M. (2008). Productive failure. *Cognition and Instruction*, 26(3),
  379–424. doi:10.1080/07370000802212669. Unscaffolded struggle on complex
  problems paid off later (comparison: well-structured problem solving).
- Renkl, A. (2014). Toward an instructionally oriented theory of
  example-based learning. *Cognitive Science*, 38(1), 1–37.
  doi:10.1111/cogs.12086. Examples work in early skill acquisition and only
  with active self-explanation; fade worked steps as expertise grows.
- Schneider, S., Beege, M., Nebel, S., & Rey, G. D. (2018). A meta-analysis
  of how signaling affects learning with media. *Educational Research
  Review*, 23, 1–24. doi:10.1016/j.edurev.2017.11.001. Cueing the relevant
  material or its organization reliably improves retention and transfer.
- Adams, D. M., McLaren, B. M., Durkin, K., Mayer, R. E., Rittle-Johnson,
  B., Isotani, S., & van Velsen, M. (2014). Using erroneous examples to
  improve mathematics learning with a web-based tutoring system. *Computers
  in Human Behavior*, 36, 401–411. doi:10.1016/j.chb.2014.03.053.
  Finding-and-fixing errors beat isomorphic supported practice on delayed
  tests.
