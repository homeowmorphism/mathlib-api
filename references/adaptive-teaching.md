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

Eight rules for composing the reply to a clarifying question, ordered as a
checklist: shape (1–3), content (4–5), depth (6), form (7), close (8). Each
carries its grounding (full citations in §Bibliography — answer style) and
its transfer caveat. The evidence is strong on direction and weak on
magnitude (§Honest limits); when rules pull against each other, rule 4
arbitrates through the learner model.

1. **Answer first, with the axis it turns on.** The opening one or two
   sentences deliver the direct answer *and* the distinction it hinges on
   ("Yes — parameter, not field; the question is who fixes the index type,
   the user or the definition"). Never make the reader scroll for the
   verdict, and never withhold it Socratically — the tutoring and AI-tutor
   evidence supports engagement *around* a delivered answer, not deferred
   ones. Grounding: an opening frame is an advance organizer at chat scale,
   with benefits concentrated on unfamiliar material (Ausubel 1960; Mayer
   1979); the reply functions as correct-answer feedback, whose retention
   value concentrates where the asker's implicit guess was wrong or absent
   (Pashler et al. 2005) — so a confirmed-correct guess earns a short
   confirmation, a wrong one the full mechanism.

2. **Diagnose out loud.** A question is the surface trace of a detected
   discrepancy — knowledge deficit, clash with a held model, or anomaly
   (Otero & Graesser 2001) — so infer which one generated it and name the
   reading in a sentence ("You're asking because X looks like it should
   behave like Y — the missing piece is Z"), then answer *that*, not a
   generic version of the question. Explicit diagnosis is the precondition
   for adapted explanation in text-mediated dialogue, and individuating
   knowledge of the asker measurably improves expert answers where generic
   audience awareness does not (Nückles, Wittwer & Renkl 2005; Wittwer,
   Nückles, Landmann & Renkl 2010; Wittwer, Nückles & Renkl 2010 — the
   closest population match in this bibliography: adults, one-on-one, text).
   Phrase the diagnosis as a reading of the question, never an assessment of
   the person, so a misdiagnosis is cheap to correct — and log it either
   way.

3. **Refute false premises in refutation-text shape.** When the question
   presupposes something false ("since bundling the index type is the
   mathlib default…"), do not answer around it: quote the presupposition —
   verbatim quoting is safe, the "familiarity backfire" is not a robust
   phenomenon (Ecker, Hogan & Lewandowsky 2017; Swire-Thompson, DeGutis &
   Lazer 2020) — tag it as incorrect, and put the correct account
   immediately adjacent, headlined as an affirmative statement of what *is*
   true (Swire, Ecker & Lewandowsky 2017). Refutation-shaped text reliably
   beats plain correct exposition (Guzzetti et al. 1993; Tippett 2010;
   Schroeder & Kucera 2022: g = 0.41 across 44 comparisons), and the
   mechanism — co-activation of the wrong and right model so the conflict is
   detected (van den Broek & Kendeou 2008) — is exactly the live situation.
   A confidently held error corrected without hedging is the error most
   likely to stay fixed (Butterfield & Metcalfe 2001). Two boundaries:
   verify the premise really is false first — a defensible alternative
   design is discussed as one, not refuted — and aim the refutation at the
   claim, with an artifact, never at the person.

4. **Calibrate per axis against the learner model; compress the known.**
   Instructional support that helps novices becomes extraneous load that
   *impairs* high-prior-knowledge learners (expertise reversal: Kalyuga et
   al. 2003; Kalyuga 2007; meta-analytically confirmed with the interaction
   strongest in higher education, Tetzlaff et al. 2025), and high-knowledge
   readers can learn more from less-elaborated text — though not for
   skilled+knowledgeable readers, so achieve brevity by omitting redundancy,
   never by loosening the argument's connectivity (McNamara et al. 1996;
   O'Reilly & McNamara 2007). Calibrate per *axis*, not globally: this
   user's mathematics is compressed to a clause; their Lean-internals
   familiarity is whatever `learner/model.md` says it is. Interesting
   asides that don't serve the question depress learning even when enjoyed
   (seductive details: Rey 2012) — offer them as pointers instead. When an
   axis's level is uncertain, include the background as a clearly marked,
   skippable aside: the meta-analytic asymmetry says over-helping an expert
   costs less than under-helping a novice (Tetzlaff et al. 2025).

5. **Mechanism, not verdict — and demonstrate rather than assert.** A bare
   verdict is the weakest feedback form: elaborated explanation beats
   correct-answer-only beats right/wrong-only, with the gap widest for
   higher-order outcomes like design judgment (Van der Kleij, Feskens &
   Eggen 2015). A bare denial is worse than weak — audiences left to
   generate their own rationale entrench the error (Chan et al. 2017), and
   in the reading-time evidence it is the causal, interconnected explanation
   of the correct account, not the refutation sentence, that eliminates a
   misconception's interference (Kendeou et al. 2014, Exp. 3). So every
   correction and every design verdict carries its causal chain — which
   tactic fails, which instance loops, which lemma becomes unstatable — and
   prefers the checkable artifact (a real goal state, a real error, a
   pinned diff) to authority. This is the conversational form of the
   example sheet's "demonstrations must be honest" rule, and it is the
   response style that separated the harm-free from the harmful arm in the
   strongest AI-tutoring RCT (Bastani et al. 2025, §Bibliography — learner
   loop).

6. **Contingent depth: resolve the live impasse, queue the rest.** Reply at
   the lowest specificity that could plausibly resolve the question —
   principle plus pointer before walkthrough — then escalate only on
   demonstrated failure and de-escalate on traction (the contingency rule:
   Wood & Middleton 1975; van de Pol et al. 2010, §Bibliography — learner
   loop). Learning concentrates at impasses; explanation past the impasse
   rarely sticks (VanLehn et al. 2003), and one exhaustive reply is a
   massed exposure — adjacent caveats and generalizations the user didn't
   ask about do more good as a brief spaced re-encounter, so route them to
   the retrieval queue instead of appending them (Cepeda et al. 2006;
   Cepeda et al. 2008, §Bibliography — learner loop). Defer only unasked
   material — never the answer itself.

7. **Signal sparsely.** When the answer has multiple points, number them
   and open each with a short label naming its content; signaling improves
   memory for what it cues while leaving the rest unaffected (Lorch 1989;
   Schneider et al. 2018: retention g+ = 0.53 — selective emphasis, not a
   global boost). Emphasis applied to everything is no emphasis: in a
   chat-length reply that means a few load-bearing labels, not heavy
   formatting — the same one-signal-per-scope discipline the example sheet
   applies to its separators.

8. **Close with one generative hook — after the answer, never instead of
   it.** End with exactly one concrete move the user can make: a prediction
   to check, a `#check` to run, a contrasting case to re-derive. The
   learner's own constructive activity, not the tutor's explanation, is the
   active ingredient of tutoring (Chi et al. 2001 — prompting matched
   explaining; ICAP: interactive > constructive > active > passive, Chi &
   Wylie 2014, §Bibliography — learner loop), the engineered-engagement
   tutor is the one that doubled gains where the answer-dispenser harmed
   them (Kestin et al. 2025; Bastani et al. 2025, §Bibliography — learner
   loop), and an attempted prediction is safe even when it fails, provided
   the answer follows (Kornell, Hays & Bjork 2009, within its verified
   scope). One hook, optional in tone, skippable without cost — for an
   expert who asked a targeted question, the hook extends the answer; it
   never gates it.

## Honest limits

- **There is no "maximally clear" style in the literature — clarity is
  learner-relative.** The central replicated finding behind rules 4 and 6
  is a *crossover interaction*: the same elaboration that helps one reader
  measurably hurts another, and the same text is redundant on one knowledge
  axis and necessary on the other. A fixed style cannot be optimal;
  principles-plus-learner-model is the literature's own prescription
  (Kalyuga 2007; Tetzlaff et al. 2025). "Maximally clear" is therefore
  implemented as: evidence-based defaults, adapted per user, checked
  against outcomes.
- **Directions transfer; magnitudes don't.** The evidence base is
  school-age and undergraduate learners in labs and classrooms reading
  prepared texts, with three exceptions closest to this setting (Nückles et
  al. 2005 and the two Wittwer et al. 2010 papers: adults, one-on-one,
  text-mediated). No published study tests an adult domain expert asking
  self-initiated clarifying questions of an AI assistant. Every rule above
  is an extrapolation by mechanism; none of the quoted effect sizes should
  be expected to reproduce here.
- **The rules trade off, and the resolution is scoped detail.** Rule 5
  demands the causal chain; rule 4 demands compression. The synthesis the
  evidence supports: spend detail on the disputed or missing mechanism,
  brevity everywhere else — detail correlated with debunking success *and*
  with misinformation persistence (Chan et al. 2017), so volume is not the
  active ingredient; targeted causal explanation is.
- **Refutation has a social cost the literature never measured.** The
  refutation-text corpus is prose, not dialogue; misdiagnosing a defensible
  position as a misconception and refuting it reads as condescension. Hence
  rule 3's verify-first boundary, and the standing rule that truth is the
  floor: never manufacture a refutation for rhetorical shape.
- **The loop is the local outcome measure.** Whether this style works *for
  this user* is an empirical question the skill can actually answer: the
  question log records whether follow-up clarifications drop and which
  register resolves questions fastest — the same outcome Wittwer et al.
  (2010) measured. When the log contradicts a rule above for this user, the
  log wins and `learner/model.md` records the override.
- **Folklore corrections worth knowing when citing this file.** Kendeou et
  al. (2014) is routinely cited as "revision requires refutation plus
  explanation" — its own Exp. 3 shows the causal explanation alone
  sufficed, so cite it for the explanation, not the refutation. Rey's
  (2012) d = 0.27/0.65 are the seductive-*text*-passages subgroup (14 and 4
  experiments), not all 39 effects. Kornell et al. (2009) equated total
  trial time only in Exps. 2, 4–6, and the time-matched pretesting
  advantage held only for weak-associate pairs (Exp. 2 was null). Bastani
  et al. (2025) is often miscited by its preprint title ("Generative AI Can
  Harm Learning" — the published harm is *without guardrails* only), and
  its engagement statistic is message-level (33% vs 63% substantive first
  interactions), not conversation-level. Kalyuga et al. (2003) is in
  *Educational Psychologist*, not *Educational Psychology Review* (that is
  Kalyuga 2007). Wood, Bruner & Ross (1976) never mentions Vygotsky and is
  descriptive, not experimental. Graesser & Person's (1994) question-rate
  factoid is quoted while dropping their actual result — quality, not
  frequency, predicted achievement. And the Wang & Fan (2025) ChatGPT
  meta-analysis (g = 0.867) has been retracted — do not cite it.

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

## Bibliography — answer style (verified 2026-08-01 by a seven-literature research pass with adversarial re-verification of every entry; one refuted finding corrected, three overstated findings rescoped — see §Honest limits, folklore corrections)

Entries already listed in §Bibliography — learner loop (Wood & Middleton
1975; Wood, Bruner & Ross 1976; van de Pol et al. 2010; VanLehn 2011;
Graesser & Person 1994; Chi & Wylie 2014; Roediger & Karpicke 2006; Rowland
2014; Cepeda et al. 2008; Kestin et al. 2025; Bastani et al. 2025) are not
repeated here.

Diagnosis and adaptation:

- Otero, J., & Graesser, A. C. (2001). PREG: Elements of a model of
  question asking. *Cognition and Instruction*, 19(2), 143–175.
  doi:10.1207/S1532690XCI1902_01. Questions are generated by detected
  discrepancies — deficits, clashes, anomalies — between input and the
  questioner's knowledge, gated by metacognition and pragmatics.
- Nückles, M., Wittwer, J., & Renkl, A. (2005). Information about a
  layperson's knowledge supports experts in giving effective and efficient
  online advice to laypersons. *Journal of Experimental Psychology:
  Applied*, 11(4), 219–236. doi:10.1037/1076-898X.11.4.219. In 80 text
  dialogues, individuating knowledge about the asker made expert advice
  more effective and efficient; generic audience sensitization did not.
- Wittwer, J., Nückles, M., Landmann, N., & Renkl, A. (2010). Can tutors be
  supported in giving effective explanations? *Journal of Educational
  Psychology*, 102(1), 74–89. doi:10.1037/a0016727. Only tutors given a
  tutee knowledge assessment customized their explanations, which deepened
  understanding, reduced false beliefs, and cut subsequent clarifying
  questions (30 dyads).
- Wittwer, J., Nückles, M., & Renkl, A. (2010). Using a diagnosis-based
  approach to individualize instructional explanations in computer-mediated
  communication. *Educational Psychology Review*, 22(1), 9–23.
  doi:10.1007/s10648-010-9118-7. In text-mediated instruction, explicit
  diagnosis of the learner's understanding is the precondition for adapted
  explanation.
- Wittwer, J., & Renkl, A. (2008). Why instructional explanations often do
  not work: A framework for understanding the effectiveness of
  instructional explanations. *Educational Psychologist*, 43(1), 49–64.
  doi:10.1080/00461520701756420. Explanations fail when unadapted to prior
  knowledge or substituting for the learner's own processing; a framework,
  not a meta-analysis.
- VanLehn, K., Siler, S., Murray, C., Yamauchi, T., & Baggett, W. B.
  (2003). Why do only some events cause learning during human tutoring?
  *Cognition and Instruction*, 21(3), 209–249.
  doi:10.1207/S1532690XCI2103_01. Across ~125 hours of physics tutoring,
  learning was uncommon unless the student was at an impasse, regardless of
  the explanations employed.

Refutation and correction:

- Guzzetti, B. J., Snyder, T. E., Glass, G. V., & Gamas, W. S. (1993).
  Promoting conceptual change in science: A comparative meta-analysis of
  instructional interventions from reading education and science education.
  *Reading Research Quarterly*, 28(2), 116–159. doi:10.2307/747886.
  Interventions that reliably corrected misconceptions shared one element —
  conceptual conflict — with refutational text among the strongest;
  standard exposition did not dislodge them.
- Tippett, C. D. (2010). Refutation text in science education: A review of
  two decades of research. *International Journal of Science and
  Mathematics Education*, 8(6), 951–970. doi:10.1007/s10763-010-9203-x.
  Refutation text is more likely than expository text to produce conceptual
  change, with no clear developmental restriction on who benefits — but not
  for all readers or all misconceptions.
- Schroeder, N. L., & Kucera, A. C. (2022). Refutation text facilitates
  learning: A meta-analysis of between-subjects experiments. *Educational
  Psychology Review*, 34(2), 957–987. doi:10.1007/s10648-021-09656-z.
  44 independent comparisons (n = 3,869): g = 0.41, consistent and robust
  across contexts.
- van den Broek, P., & Kendeou, P. (2008). Cognitive processes in
  comprehension of science texts: The role of co-activation in confronting
  misconceptions. *Applied Cognitive Psychology*, 22(3), 335–351.
  doi:10.1002/acp.1418. Refutation works by co-activating the misconception
  and the correct account so the conflict is detected and resolved.
- Kendeou, P., Walsh, E. K., Smith, E. R., & O'Brien, E. J. (2014).
  Knowledge revision processes in refutation texts. *Discourse Processes*,
  51(5–6), 374–397. doi:10.1080/0163853X.2014.913961. Refutation alone
  reduced but did not eliminate a misconception's reading disruption; a
  causal, interconnected explanation of the correct account eliminated it
  with or without the explicit refutation (Exp. 3) — cite for the
  explanation, not the refutation.
- Chan, M. S., Jones, C. R., Hall Jamieson, K., & Albarracín, D. (2017).
  Debunking: A meta-analysis of the psychological efficacy of messages
  countering misinformation. *Psychological Science*, 28(11), 1531–1546.
  doi:10.1177/0956797617714579. Debunking effects were large (ds =
  1.14–1.33) but so was persistence (ds = 0.75–1.06), worst when audiences
  generated their own reasons supporting the misinformation; the
  detail–debunking link is a between-study correlation.
- Ecker, U. K. H., Hogan, J. L., & Lewandowsky, S. (2017). Reminders and
  repetition of misinformation: Helping or hindering its retraction?
  *Journal of Applied Research in Memory and Cognition*, 6(2), 185–192.
  doi:10.1016/j.jarmac.2017.01.014. Retractions explicitly repeating the
  misinformation reduced its influence *more* than retractions avoiding
  repetition.
- Swire, B., Ecker, U. K. H., & Lewandowsky, S. (2017). The role of
  familiarity in correcting inaccurate information. *Journal of
  Experimental Psychology: Learning, Memory, and Cognition*, 43(12),
  1948–1961. doi:10.1037/xlm0000422. Corrections repeating myths still
  reduced belief with no immediate backfire; fact affirmations and greater
  explanatory detail produced the more sustained change.
- Swire-Thompson, B., DeGutis, J., & Lazer, D. (2020). Searching for the
  backfire effect: Measurement and design considerations. *Journal of
  Applied Research in Memory and Cognition*, 9(3), 286–299.
  doi:10.1016/j.jarmac.2020.06.006. Familiarity and worldview backfire
  effects are not robust phenomena; apparent backfires are often
  measurement artifacts.
- Butterfield, B., & Metcalfe, J. (2001). Errors committed with high
  confidence are hypercorrected. *Journal of Experimental Psychology:
  Learning, Memory, and Cognition*, 27(6), 1491–1494. High-confidence
  errors given correct-answer feedback were more likely to be corrected at
  retest than low-confidence errors.

Calibration and expository clarity:

- Kalyuga, S., Ayres, P., Chandler, P., & Sweller, J. (2003). The expertise
  reversal effect. *Educational Psychologist*, 38(1), 23–31.
  doi:10.1207/S15326985EP3801_4. Guidance that helps novices becomes
  redundant load that can impair knowledgeable learners; a narrative review
  (venue trap: not *Educational Psychology Review*).
- Kalyuga, S. (2007). Expertise reversal effect and its implications for
  learner-tailored instruction. *Educational Psychology Review*, 19(4),
  509–539. doi:10.1007/s10648-007-9054-3. Consolidates the effect as an
  aptitude–treatment interaction and prescribes dynamic tailoring to
  current knowledge.
- Tetzlaff, L., Simonsmeier, B., Peters, T., & Brod, G. (2025). A
  cornerstone of adaptivity — A meta-analysis of the expertise reversal
  effect. *Learning and Instruction*, 98, 102142.
  doi:10.1016/j.learninstruc.2025.102142. 60 studies, N = 5,924: the
  interaction confirmed, strongest in higher education, and asymmetric —
  assisting novices helps more than over-assisting experts hurts.
- McNamara, D. S., Kintsch, E., Songer, N. B., & Kintsch, W. (1996). Are
  good texts always better? *Cognition and Instruction*, 14(1), 1–43.
  doi:10.1207/s1532690xci1401_1. High-knowledge readers learned more from
  minimally coherent text, low-knowledge readers from coherent text — on
  deep measures only (the reverse cohesion effect).
- O'Reilly, T., & McNamara, D. S. (2007). Reversing the reverse cohesion
  effect: Good texts can be better for strategic, high-knowledge readers.
  *Discourse Processes*, 43(2), 121–152. doi:10.1080/01638530709336895.
  The low-cohesion advantage held only for less-skilled high-knowledge
  readers; skilled comprehenders profited from high cohesion — so omit
  redundancy, never connectivity.
- Rey, G. D. (2012). A review of research and a meta-analysis of the
  seductive detail effect. *Educational Research Review*, 7(3), 216–237.
  doi:10.1016/j.edurev.2012.05.003. Interesting-but-irrelevant additions
  depress learning; the oft-quoted d = 0.27 (retention) and d = 0.65
  (transfer) are the seductive-text-passages subgroup (14 and 4
  experiments), not all 39 effects.

Structure and signaling:

- Ausubel, D. P. (1960). The use of advance organizers in the learning and
  retention of meaningful verbal material. *Journal of Educational
  Psychology*, 51(5), 267–272. doi:10.1037/h0046669. A short
  superordinate-ideas organizer before an unfamiliar technical passage
  improved retention over a same-length historical introduction.
- Mayer, R. E. (1979). Can advance organizers influence meaningful
  learning? *Review of Educational Research*, 49(2), 371–383.
  doi:10.3102/00346543049002371. Organizers facilitate learning in
  appropriate situations — unfamiliar or poorly organized material,
  low-prior-knowledge learners — with effects strongest on transfer.
- Lorch, R. F., Jr. (1989). Text-signaling devices and their effects on
  reading and memory processes. *Educational Psychology Review*, 1(3),
  209–234. doi:10.1007/BF01320135. Virtually all signal types improve
  memory for the cued information while leaving unsignaled information
  unaffected — selective emphasis, not a global boost.
- Schneider, S., Beege, M., Nebel, S., & Rey, G. D. (2018). A meta-analysis
  of how signaling affects learning with media. *Educational Research
  Review*, 23, 1–24. doi:10.1016/j.edurev.2017.11.001. 103 studies,
  N = 12,201: retention g+ = 0.53, transfer g+ = 0.33, with reduced load
  and better-targeted attention as mechanisms.

Feedback, interactivity, and the close:

- Van der Kleij, F. M., Feskens, R. C. W., & Eggen, T. J. H. M. (2015).
  Effects of feedback in a computer-based learning environment on students'
  learning outcomes: A meta-analysis. *Review of Educational Research*,
  85(4), 475–511. doi:10.3102/0034654314564881. Elaborated explanatory
  feedback (ES = 0.49) beats correct-answer-only (0.32) beats right/wrong
  verdicts (0.05), with the gap widest for higher-order outcomes.
- Pashler, H., Cepeda, N. J., Wixted, J. T., & Rohrer, D. (2005). When does
  feedback facilitate learning of words? *Journal of Experimental
  Psychology: Learning, Memory, and Cognition*, 31(1), 3–8.
  doi:10.1037/0278-7393.31.1.3. Correct-answer feedback after errors
  carried nearly all the retention gain; feedback after correct responses
  added almost nothing, regardless of confidence.
- Chi, M. T. H., Siler, S. A., Jeong, H., Yamauchi, T., & Hausmann, R. G.
  (2001). Learning from human tutoring. *Cognitive Science*, 25(4),
  471–533. doi:10.1016/S0364-0213(01)00044-1. Students learned just as
  effectively when tutors were restricted to content-free prompting as
  with unrestricted explaining — the learner's construction, not the
  explanation, is the active ingredient.
- Kornell, N., Hays, M. J., & Bjork, R. A. (2009). Unsuccessful retrieval
  attempts enhance subsequent learning. *Journal of Experimental
  Psychology: Learning, Memory, and Cognition*, 35(4), 989–998.
  doi:10.1037/a0015729. Failed attempts followed by the answer beat
  reading in all six experiments, but time was equated only in Exps. 2 and
  4–6, where the advantage held for weak-associate pairs and was null for
  fictional trivia — cite within this scope.
- Karpicke, J. D., & Blunt, J. R. (2011). Retrieval practice produces more
  learning than elaborative studying with concept mapping. *Science*,
  331(6018), 772–775. doi:10.1126/science.1199327. One retrieval session
  beat 25 minutes of concept mapping on a 1-week test (0.67 vs 0.45,
  d = 1.50) while learners predicted the opposite; the comparison is to
  concept mapping specifically.
- Cepeda, N. J., Pashler, H., Vul, E., Wixted, J. T., & Rohrer, D. (2006).
  Distributed practice in verbal recall tasks: A review and quantitative
  synthesis. *Psychological Bulletin*, 132(3), 354–380.
  doi:10.1037/0033-2909.132.3.354. 839 assessments across 317 experiments:
  spaced beats massed, and the optimal gap grows with the retention
  interval.
- Kulik, J. A., & Fletcher, J. D. (2016). Effectiveness of intelligent
  tutoring systems: A meta-analytic review. *Review of Educational
  Research*, 86(1), 42–78. doi:10.3102/0034654315581420. Step-level
  tutoring raised scores a median 0.66 SD across 50 evaluations — with
  gains heavily moderated by locally developed vs standardized tests.
- Wu, X., Zhu, P., Zhang, J., Yin, M., & Wang, Y. (2026). ChatGPT's impact
  on student learning outcomes: A meta-analysis of 35 experimental studies.
  *Humanities and Social Sciences Communications*, 13, 684.
  doi:10.1057/s41599-026-07019-z. 35 experiments, 4,193 participants:
  overall g = 0.670 on learning outcomes, moderated by duration and
  subject.
