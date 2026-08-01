# Adaptive teaching — the learner loop and the answer style

This file is the full protocol behind SKILL.md's "learner loop" section: how
clarifying questions update a persistent learner model, and how answers to
those questions are written. The second half is the **answer-style protocol**
— the evidence-based rules for composing a maximally clear chat answer —
with its own verified bibliography, mirroring the example-sheet reference's
treatment of the sheet format.

Scope note: `references/example-sheet.md` governs the *artifact* (the
contrasting-cases `.lean` file); this file governs the *conversation* — what
to do when the user asks a question, in the moment and across sessions.

## The two-tier design

What the skill learns splits by whom it is about:

- **`learner/` (personal, gitignored, never committed)** — what the skill
  knows *about this user*: the verbatim question log and the consolidated
  learner model. Private to the installation.
- **`references/` (tracked, committed)** — what the user's questions reveal
  about *the skill's own text*: a missing prerequisite, an explanation anyone
  would stumble on, a dangling pointer. These are defects; fix the reference
  file and commit, citing the log entry date in the commit message.

The learner model is an **open** model: the user may read, correct, or edit
`learner/` at any time, and substantive new inferences are surfaced to the
user for confirmation before they harden into "knows well" entries.

## The files and their schemas

First use: `learner/` is gitignored, so a fresh clone does not ship it. If
the directory is missing, create both files from the templates below before
proceeding — an empty log and a model whose sections are empty except for
clearly labeled initial priors, if any.

### `learner/question-log.md` — append-only, verbatim

Every clarifying question asked while the skill is active gets an entry,
appended in the same turn it is answered. The question is quoted *verbatim* —
the citation discipline applies to the user's words too. Entries are never
rewritten or deleted; consolidation happens in `model.md`, which links back.

```markdown
## YYYY-MM-DD · <phase or context> · <diagnosis tag>
Q: "<the question, verbatim>"
Diagnosis: <which gap the question reveals — missing prerequisite /
  explanation pitched at the wrong level / terminology gap / genuine open
  design question / skill-text defect>
Resolved by: <the explanation or demonstration that visibly resolved it —
  what actually worked, not what was tried first>
Anticipate: <the rule for next time — what to preempt, and where>
Retrieval: no | yes — "<one-line prompt to re-ask later>"
```

Diagnosis tags are descriptive, never evaluative: they name the gap between
the explanation and the reader, not a deficiency in the reader. Header tags
are free-form with one exception: when the diagnosis is a defect in the
skill's own text, the tag is the verbatim string `skill-text defect` —
consolidation greps for exactly that string to route the committed fix.

### `learner/model.md` — consolidated, read first

Read at every invocation, before Phase 0. Five sections, each with its own
evidence discipline:

- **Knows well — stop explaining.** Entries move here only after evidence:
  fluent use, a correct unprompted paraphrase, or explicit confirmation —
  never after a single non-question. Material here is skipped or compressed;
  re-explaining it costs attention (expertise reversal, §Answer style rule 4).
- **Active gaps.** Each links the log entries that revealed it. A gap closes
  when a retrieval prompt or later usage shows it resolved. Single-datum
  gaps are marked as such and not hardened.
- **Question patterns → anticipation rules.** Promoted after ≥2 related log
  entries. Each rule names where to preempt: dossier prose, example-sheet
  docstring, `Says:` line, or a reference-file fix.
- **Explanation styles — what works, what doesn't.** Evidence-pinned
  observations about which register resolved this user's questions fastest.
- **Retrieval queue.** Dated, not session-counted — sessions are irregular
  and the spacing evidence is about elapsed time. Each prompt carries a due
  date and a recall tally. Schedule: first re-ask at least a day out, then
  ~a week, then ~a month; retire after two clean recalls; a failed recall
  gets a re-explanation and a reset to due-tomorrow. At most ONE due prompt
  is posed per session, at a natural pause, always skippably. (Grounding:
  §Why the learner loop, the three retrieval bullets.)

Every model claim must pin to a dated log entry; a claim with no log evidence
is labeled a guess.

The model opens with a `Consolidated through:` line naming the last log
entry it has absorbed. At invocation, if `question-log.md` holds entries
newer than that line, consolidate them before Phase 0 — the catch-up path
for sessions that ended without consolidating. Template (also what to
create on first use):

```markdown
# Learner model (personal — not tracked by git)

Consolidated through: <date · context of last absorbed entry, or "nothing yet">

## Knows well — stop explaining
## Active gaps
## Question patterns → anticipation rules
## Explanation styles — what works, what doesn't
## Retrieval queue
(entry format: `due YYYY-MM-DD · "<prompt>" · from <log date> · recalls n/2`)
```

### The loop, per turn and per session

1. **At invocation**: read `learner/model.md`; if the log holds entries
   newer than its `Consolidated through:` line, consolidate them first; then
   apply its skips, preemptions, and (at most one) due retrieval prompt.
2. **On each clarifying question**: diagnose → answer per the answer-style
   protocol below → append the log entry, same turn.
3. **Consolidate** before session end or every ~3 new entries: update
   `model.md` from the log; commit any reference-file fixes the entries
   revealed. `learner/` itself is never committed.

## Why the learner loop — the learning-science grounding

Each element of the loop instantiates a verified finding (full citations in
§Bibliography — learner loop):

- **A tutor runs a theory of the tutee.** The paper that coined
  "scaffolding" says it in so many words: the effective tutor needs two
  models, a theory of the task and "a theory of the performance
  characteristics of his tutee" — without both, "he can neither generate
  feedback nor devise situations in which his feedback will be more
  appropriate for this tutee in this task at this point in task mastery"
  (Wood, Bruner & Ross 1976). The computational precedent is knowledge
  tracing: a persistent per-skill estimate of what the student has learned,
  used to decide what to present next (Corbett & Anderson 1995). Hence
  `learner/model.md`, read before Phase 0 of every invocation.
- **Questions localize the frontier of knowledge.** Question asking peaks
  where a learner's knowledge meets the material — "to ask a question, one
  must know enough to know what is not known" (Miyake & Norman 1979; an
  interaction effect, not "novices ask more"). One-on-one tutoring is where
  the signal surfaces: student questions are roughly 240× as frequent as in
  classrooms, and it is question *quality*, not frequency, that tracks
  achievement (Graesser & Person 1994). Hence: log the question verbatim and
  diagnose the gap it reveals — the question's content and position are the
  data.
- **Diagnose first, then calibrate; recalibrate as the learner changes.**
  Contingency — more support after failure, less after success, concentrated
  on the learner's "region of sensitivity to instruction" — is what
  separated effective from ineffective tutors in the founding study (Wood &
  Middleton 1975), and the decade review makes diagnosis the definitional
  tool of scaffolding: "one must first determine the student's current level
  of competence; only with such knowledge can the support be adapted" (van
  de Pol, Volman & Beishuizen 2010). Adaptation works in software too: a
  rapid knowledge probe can select the right instructional format in real
  time (Kalyuga & Sweller 2004), and fading support on an individually
  adaptive schedule beats the same fading on a fixed one (Salden et al.
  2010). Hence the diagnosis tags, and a "knows well" section that retires
  explanations the user has outgrown (the adaptive complement of
  `example-sheet.md`'s expertise-reversal entry, Kalyuga et al. 2003).
- **Answer contingently, not just correctly.** Unguarded GPT-4 access raised
  assisted practice performance 48% but *lowered* subsequent unassisted exam
  performance 17%; a tutor variant constrained to hints and teacher-informed
  feedback neutralized the harm (Bastani et al. 2025). In a 900-tutor RCT,
  an LLM layer nudging human tutors toward guiding questions and away from
  answer-giving raised student mastery by 4 percentage points (Wang et al.
  2024). Engagement mode matters independently: learning increases from
  passive through active and constructive to interactive engagement (Chi &
  Wylie 2014, ICAP). Hence: diagnose, pitch to the diagnosis, close
  interactively where a natural opening exists. The rule calibrates, never
  gatekeeps: read *who is asking* from `learner/model.md` — the model, not
  this file, records where the user's expertise lies — answer the question
  asked, show the reasoning and the failing goal rather than the bare
  conclusion, and never withhold an answer to quiz.
- **Re-asking beats re-explaining.** Retrieval practice outperforms restudy
  for delayed retention (Roediger & Karpicke 2006; overall g = 0.50, larger
  for recall-format prompts and at delays beyond a day, Rowland 2014), and
  practice testing plus distributed practice are the only two techniques
  rated high-utility in the field's major review — while rereading,
  re-explanation's nearest analogue, rates low (Dunlosky et al. 2013).
  Hence the retrieval queue: an answered question worth keeping becomes a
  prompt to re-ask, not a paragraph to repeat.
- **Space the re-asks at widening intervals, few and cheap.** The optimal
  gap between encounters grows with the desired retention interval (Cepeda
  et al. 2008), and the best durability-per-trial schedule is recall to
  criterion, then a few widely spaced relearnings — extra same-session
  practice is subadditive (Rawson & Dunlosky 2011, successive relearning).
  Hence the queue schedule, in elapsed time because that is what the
  evidence measures: at least a day, then ~a week, then ~a month, retired
  after two clean recalls.
- **Retrieval strengthens what was practiced — claim no more.** Transfer
  from retrieval practice exists but is conditional (d = 0.40 overall,
  robust with elaborated feedback and high initial success, weak or absent
  for untested material; Pan & Rickard 2018). Hence: re-ask the user's *own
  logged questions*, with elaborated feedback, and never expect the queue to
  teach material it doesn't contain.
- **The model is open.** Learner models made visible to the learner are the
  canonical design for supporting reflection, improving model accuracy, and
  building trust (Bull & Kay 2007; revised in Bull & Kay 2016 — a design
  framework, not an efficacy trial). Hence: `learner/model.md` is plain
  markdown the user may read, correct, or edit, and substantive inferences
  are surfaced before they harden.
- **Pedagogy is steerable through instructions; the model is the vehicle.**
  Teaching behavior in LLMs is trainable and steerable as "pedagogical
  instruction following" (LearnLM Team 2024), and is properly evaluated on
  pedagogical dimensions — adapting to the learner, checking understanding —
  not answer quality alone (Jurenka et al. 2024). This file *is* the
  pedagogical instruction; the learner model is what makes it per-user and
  cross-session.

**Honest bounds.** No published study tests this exact mechanism — logging a
learner's clarifying questions into a cross-session model is an
extrapolation from the evidence above, not a replicated finding. The
realistic ceiling for tutoring gains is about 0.8 SD, not Bloom's folklore
two sigma (VanLehn 2011: human tutoring d = 0.79, step-based tutoring
systems d = 0.76, finer granularity added nothing). The scaffolding
effectiveness base is thin, with measurement its main challenge (van de Pol
et al. 2010). And GenAI tutoring effects are moderate on average with very
high heterogeneity — implementation design, not model access, decides the
outcome (Han, Peng & Liu 2025: SMD = 0.45, I² = 95%). The foundational
scaffolding studies are small and observational (Wood & Middleton 1975 is
correlational, 12 dyads; Wood, Bruner & Ross 1976 is descriptive, no
control condition).

## The answer-style protocol

<!-- PENDING: rules grounded in the verified bibliography — filled in from
the answer-style-evidence research fleet (run wf_3f75ee18-214, 2026-08-01).
Do not cite this section until the PENDING markers are gone. -->

## Honest limits

<!-- PENDING: moderators, evidence populations, and why "maximally clear"
is a set of principles plus an adaptive model, not a unique optimum. -->

## Bibliography — learner loop (verified 2026-08-01 against Crossref/publisher/arXiv records; one-line findings faithful to the papers, caveats included)

Contingent scaffolding and adaptive instruction:

- Wood, D., & Middleton, D. (1975). A study of assisted problem-solving.
  *British Journal of Psychology*, 66(2), 181–191.
  doi:10.1111/j.2044-8295.1975.tb01454.x. Mothers whose help shifted
  contingently on the child's response — more control after failure, less
  after success — had children who later performed best alone;
  correlational, 12 dyads.
- Wood, D., Bruner, J. S., & Ross, G. (1976). The role of tutoring in
  problem solving. *Journal of Child Psychology and Psychiatry*, 17(2),
  89–100. doi:10.1111/j.1469-7610.1976.tb00381.x. Coined "scaffolding"; the
  effective tutor runs a theory of the task and "a theory of the
  performance characteristics of his tutee"; descriptive, no control
  condition.
- van de Pol, J., Volman, M., & Beishuizen, J. (2010). Scaffolding in
  teacher–student interaction: A decade of research. *Educational
  Psychology Review*, 22(3), 271–296. doi:10.1007/s10648-010-9127-6.
  Contingency, fading, and transfer of responsibility define scaffolding,
  with diagnosis as contingency's tool; effectiveness studies few,
  measurement the main challenge.
- Kalyuga, S., & Sweller, J. (2004). Measuring knowledge to optimize
  cognitive load factors during instruction. *Journal of Educational
  Psychology*, 96(3), 558–568. doi:10.1037/0022-0663.96.3.558. A rapid
  schema-based knowledge probe (r up to .92 with traditional tests at
  2.5–4.9× less testing time) can select the instructional format fitting
  current expertise in real time.
- Salden, R. J. C. M., Aleven, V., Schwonke, R., & Renkl, A. (2010). The
  expertise reversal effect and worked examples in tutored problem solving.
  *Instructional Science*, 38(3), 289–307. doi:10.1007/s11251-009-9107-8.
  Lab and classroom experiments: adaptive fading of worked examples beat
  fixed fading, which beat tutored problem solving.
- VanLehn, K. (2011). The relative effectiveness of human tutoring,
  intelligent tutoring systems, and other tutoring systems. *Educational
  Psychologist*, 46(4), 197–221. doi:10.1080/00461520.2011.611369. Human
  tutoring d = 0.79 (not Bloom's folklore 2.0); step-based tutoring systems
  d = 0.76; sub-step granularity added no further gain.

Question-asking and learner models:

- Miyake, N., & Norman, D. A. (1979). To ask a question, one must know
  enough to know what is not known. *Journal of Verbal Learning and Verbal
  Behavior*, 18(3), 357–364. doi:10.1016/S0022-5371(79)90200-7. Question
  asking peaked where knowledge matched material — novices asked more on
  easy material, trained learners more on hard — an interaction, not a
  simple novice effect.
- Graesser, A. C., & Person, N. K. (1994). Question asking during tutoring.
  *American Educational Research Journal*, 31(1), 104–137.
  doi:10.3102/00028312031001104. Student questions ~240× as frequent in
  tutoring as in classrooms; achievement correlated with question quality
  (after some tutoring experience), not frequency.
- Chi, M. T. H., & Wylie, R. (2014). The ICAP framework: Linking cognitive
  engagement to active learning outcomes. *Educational Psychologist*,
  49(4), 219–243. doi:10.1080/00461520.2014.965823. Learning increases from
  passive to active to constructive to interactive engagement; a framework
  with a literature synthesis, not a meta-analysis.
- Corbett, A. T., & Anderson, J. R. (1995). Knowledge tracing: Modeling the
  acquisition of procedural knowledge. *User Modeling and User-Adapted
  Interaction*, 4(4), 253–278. doi:10.1007/BF01099821. A persistent
  per-rule probability of mastery drives individualized exercise sequencing;
  the authors report the model "quite successful in predicting test
  performance".
- Bull, S., & Kay, J. (2007). Student models that invite the learner in:
  The SMILI:() open learner modelling framework. *International Journal of
  Artificial Intelligence in Education*, 17(2), 89–120.
  doi:10.3233/IRG-2007-17(2)02. The canonical framework for learner models
  made visible to the learner (reflection, accuracy, trust); descriptive,
  not an efficacy trial.
- Bull, S., & Kay, J. (2016). SMILI☺: A framework for interfaces to
  learning data in open learner models, learning analytics and related
  fields. *International Journal of Artificial Intelligence in Education*,
  26(1), 293–331. doi:10.1007/s40593-015-0090-8. The revision candidly
  notes the original was little used as an analysis tool though it shaped
  open-learner-model design; simplified guide for designers.

Retrieval practice and spacing:

- Roediger, H. L., III, & Karpicke, J. D. (2006). Test-enhanced learning:
  Taking memory tests improves long-term retention. *Psychological
  Science*, 17(3), 249–255. doi:10.1111/j.1467-9280.2006.01693.x. Restudy
  won at 5 minutes; prior testing won at 2 days and 1 week — while restudy
  inflated confidence.
- Rowland, C. A. (2014). The effect of testing versus restudy on retention:
  A meta-analytic review of the testing effect. *Psychological Bulletin*,
  140(6), 1432–1463. doi:10.1037/a0037559. Overall g = 0.50 vs restudy;
  larger for recall-format prompts (cued recall g = 0.61 vs recognition
  0.29) and at delays of a day or more (g = 0.69).
- Cepeda, N. J., Vul, E., Rohrer, D., Wixted, J. T., & Pashler, H. (2008).
  Spacing effects in learning: A temporal ridgeline of optimal retention.
  *Psychological Science*, 19(11), 1095–1102.
  doi:10.1111/j.1467-9280.2008.02209.x. Optimal inter-study gap grows with
  the retention interval in absolute terms while shrinking as a proportion
  of it (~20–40% at 1 week down to ~5–10% at 1 year).
- Dunlosky, J., Rawson, K. A., Marsh, E. J., Nathan, M. J., & Willingham,
  D. T. (2013). Improving students' learning with effective learning
  techniques: Promising directions from cognitive and educational
  psychology. *Psychological Science in the Public Interest*, 14(1), 4–58.
  doi:10.1177/1529100612453266. Of 10 techniques, only practice testing and
  distributed practice rate high utility; rereading — re-explanation's
  nearest analogue — rates low.
- Rawson, K. A., & Dunlosky, J. (2011). Optimizing schedules of retrieval
  practice for durable and efficient learning: How much is enough? *Journal
  of Experimental Psychology: General*, 140(3), 283–302.
  doi:10.1037/a0023956. Recall to criterion once, then ~3 widely spaced
  relearning sessions, is the best durability-per-trial schedule; initial
  overpractice is subadditive.
- Pan, S. C., & Rickard, T. C. (2018). Transfer of test-enhanced learning:
  Meta-analytic review and synthesis. *Psychological Bulletin*, 144(7),
  710–756. doi:10.1037/bul0000151. Transfer d = 0.40 vs restudy, but
  conditional — robust with elaborated retrieval and high initial success,
  weak or absent for untested material.

LLM tutoring, 2024–2026:

- Wang, R. E., Ribeiro, A. T., Robinson, C. D., Loeb, S., & Demszky, D.
  (2024). Tutor CoPilot: A human-AI approach for scaling real-time
  expertise. EdWorkingPaper 24-1054, Annenberg Institute at Brown (not
  NBER, contrary to common citation); arXiv:2410.03017; not yet
  peer-reviewed. RCT, 900 tutors / 1,800 underserved K-12 students: LLM
  guidance toward probing questions (not answers) raised topic mastery
  4 p.p., 9 p.p. under lower-rated tutors.
- Kestin, G., Miller, K., Klales, A., Milbourne, T., & Ponti, G. (2025). AI
  tutoring outperforms in-class active learning: An RCT introducing a novel
  research-based design in an authentic educational setting. *Scientific
  Reports*, 15, 17458. doi:10.1038/s41598-025-97652-6. Crossover RCT, 194
  Harvard physics students, two lessons: a tutor whose prompt encoded
  scaffolding, engagement, and cognitive-load management roughly doubled
  learning gains vs an active-learning class; one course, and the
  comparator is a class, not human tutoring.
- Bastani, H., Bastani, O., Sungu, A., Ge, H., Kabakcı, Ö., & Mariman, R.
  (2025). Generative AI without guardrails can harm learning: Evidence from
  high school mathematics. *Proceedings of the National Academy of
  Sciences*, 122(26), e2422633122. doi:10.1073/pnas.2422633122. Vanilla
  GPT-4 access: +48% assisted practice, −17% unassisted exam; a hints-only,
  teacher-informed tutor neutralized the harm (it did not produce an
  unassisted gain).
- LearnLM Team, Google (2024). LearnLM: Improving Gemini for learning.
  Technical report, arXiv:2412.16429; not peer-reviewed. Frames
  pedagogy-tuning as "pedagogical instruction following" — teaching
  behavior steered by system-level instructions rather than one baked-in
  pedagogy.
- Jurenka, I., et al. (2024). Towards responsible development of generative
  AI for education: An evaluation-driven approach. Technical report,
  arXiv:2407.12687; not peer-reviewed. Translates learning-science
  principles into seven pedagogy benchmarks; outcomes are educator/learner
  preferences on pedagogical dimensions, not learning-outcome effects.
- Han, X., Peng, H., & Liu, M. (2025). The impact of GenAI on learning
  outcomes: A systematic review and meta-analysis of experimental studies.
  *Educational Research Review*, 48, 100714.
  doi:10.1016/j.edurev.2025.100714. 68 studies, 337 effect sizes:
  SMD = 0.45 with I² = 95% — implementation design, not model access,
  drives the outcome.

## Bibliography — answer style

<!-- PENDING: verified citations only, with DOIs and one-line findings
faithful to the papers, per the example-sheet reference's citation
contract; filled in from the answer-style-evidence research fleet (run
wf_3f75ee18-214, 2026-08-01). -->
