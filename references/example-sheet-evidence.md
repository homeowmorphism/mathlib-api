# The example sheet — learning-science evidence base

**Load policy: not needed to build a sheet** — the operational recipe and
contracts are in `example-sheet.md`. Load this file only when the format
itself is questioned (by the user or a reviewer) or amended, or when a full
citation must be quoted.

## Why this format

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
- **Caps names; bold only in the separators.** Give the contrasted styles
  short caps names (FIELD / PARAM), introduce them in the module doc, and
  reserve bold for the separator blocks (`/-! **FIELD** -/`) that chunk
  each test. Everywhere else — prose, docstrings, headers — the caps name
  alone is the label: CAPS already reads as a marker, and a cue works by
  *selective* emphasis (signaling principle: Schneider et al. 2018
  meta-analysis); emphasis applied to every mention is no emphasis at all.
  Never bold inside code, identifiers, or verbatim quotes.
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

## Prose readability (sentence level)

The rules above govern the sheet's structure; these govern the sentences
inside its comment blocks. Added 2026-08-01, after user feedback that the
prose was "so LLM-y that they are overwhelming to read".

- **Short sentences, one idea each.** Comprehension falls as sentence
  length rises; sentence length is one of the two variables in the
  classical readability formulas (Flesch 1948).
- **No mid-sentence interruptions.** An em-dash aside or a nested
  parenthetical is a center-embedded constituent: the reader holds the
  suspended clause in working memory for the length of the intrusion
  (Gibson 1998). Prefer a second sentence.
- **Active voice; verbs over nominalizations.** Passives take longer to
  verify than actives (Gough 1965); prose rewritten from nominalizations
  into active verbs is more comprehensible (Coleman 1964).
- **Given before new.** Sentences integrate faster when they open from
  already-established material (Haviland & Clark 1974). This also rules
  out inverted openings ("Nor was X a one-off").
- **No decorative emphasis.** Signaling works by selectivity (Schneider et
  al. 2018, above), and interesting-but-irrelevant flourishes measurably
  hurt learning (the seductive-detail effect: Rey 2012).

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

### §Prose readability entries

Added 2026-08-01. Coleman 1964 and Rey 2012 were verified against
publisher/index records that day; Flesch, Gough, Gibson, and Haviland &
Clark are cited from standard bibliographic knowledge — re-verify before
quoting externally.

- Flesch, R. (1948). A new readability yardstick. *Journal of Applied
  Psychology*, 32(3), 221–233. Reading ease as a function of sentence
  length and word length.
- Coleman, E. B. (1964). The comprehensibility of several grammatical
  transformations. *Journal of Applied Psychology*, 48(3), 186–190. Four
  experiments; active-verb transforms beat nominalizations and passives.
- Gough, P. B. (1965). Grammatical transformations and speed of
  understanding. *Journal of Verbal Learning and Verbal Behavior*, 4(2),
  107–111. Passives and negatives take longer to verify than actives.
- Haviland, S. E., & Clark, H. H. (1974). What's new? Acquiring new
  information as a process in comprehension. *Journal of Verbal Learning
  and Verbal Behavior*, 13(5), 512–521. Sentences are understood faster
  when given information precedes new.
- Gibson, E. (1998). Linguistic complexity: Locality of syntactic
  dependencies. *Cognition*, 68(1), 1–76. Processing cost grows with the
  distance over which an incomplete dependency is held open.
- Rey, G. D. (2012). A review of research and a meta-analysis of the
  seductive detail effect. *Educational Research Review*, 7(3), 216–237.
  doi:10.1016/j.edurev.2012.05.003. Interesting but irrelevant additions
  reliably reduce retention and transfer.
