# Epistemics

How to reason about confidence when evidence is historical, fragmentary, and sometimes contradictory, and how to communicate it without flattening it into false certainty.

Code and math don't carry their own motivation. You can read what a function computes or what an equation assumes. You can't read *why it was chosen*. That lives in commits, notes, drafts, runs, messages, and agent sessions, all incomplete, biased, and sometimes missing. Pretending otherwise produces confident-sounding guesses that mislead the researcher.

## Recorded vs Technical Rationale

Every research "why" has two layers. Keep them in separate sections of the output.

- **Recorded rationale** answers "why was this chosen?" Evidence is something written at or before the time of the decision: a commit message, a note, a run annotation, a message, a draft paragraph, a user message in an agent session.
- **Technical rationale** answers "what makes this choice sound or unsound?" Evidence is a theorem, a paper, a reference implementation, a result, or a check you performed. It can be found today.

A technical rationale can be correct and still not be the reason. The step size may satisfy the convergence theorem while having been copied from a tutorial. Report both, and never let one stand in for the other.

## Confidence Tiers

Every claim about the recorded rationale sits in one of these tiers. The tier determines which output section it goes in and how it's phrased.

### 1. Direct

An explicit, textual statement that answers the question. Not "the math requires X so the author must have wanted X." Something someone actually *wrote* that says why.

Examples:
- A commit message: "switch MSE to Huber, MSE diverges on the outliers in split B (run 0412)"
- A comment: "# step size 1/L, needed for the O(1/k) rate in Beck & Teboulle 2009, Thm 3.1"
- A note: "LP relaxation instead of Lagrangian: Gurobi solves it in under a second at our scale"
- A W&B run note: "lr=1e-3 unstable after 20k steps, dropping to 3e-4"
- An advisor's message: "drop the synthetic benchmark, reviewers won't care"
- A user message in an agent session: "fix the seed per fold, the plots have to match the paper"

Phrasing: confident, present tense. "This exists because X." Cite the source.

### 2. Supported

Several pieces of indirect evidence converge. No single source states it, but the pattern makes it likely.

Examples:
- The constant changed three times in an hour, each change followed by a run, and the final value matches the best run of that sweep
- A reviewer asked for baseline X, and the commit adding X is dated two days later, just before the rebuttal deadline
- The code, the draft's appendix, and the reference implementation all use the same unusual constant

Phrasing: confident but clearly derived. "The evidence points strongly to X: [the specific pieces]." Cite multiple sources.

### 3. Inferred

A reasonable reading of the context that nothing explicitly supports. The reader should see it is *your interpretation*.

Examples:
- The commit says "fix", but the preceding run in the logs ended in NaN and the change adds an epsilon inside the log. Likely a stability fix.
- The value 0.99 matches the default in the reference implementation the repo appears to have started from. Likely inherited.

Phrasing: hedged. "It appears", "likely", "suggests", "is consistent with", "one reading is". Make the chain explicit: "Given A and B, C seems likely because D."

### 4. Speculative

A plausible hypothesis with thin evidence, where other explanations fit equally well. Worth presenting, clearly marked as a guess.

Examples:
- "The 300s time limit might match the cluster queue's job limit, but no config or note references it."
- "The 80/20 split may follow prior work, but none of the cited papers uses it."

Phrasing: explicitly speculative. "One possibility is X, but we have no direct evidence." Usually lives in "Competing Hypotheses."

### 5. Unknown

You looked and couldn't find out. A valid and important outcome.

Phrasing: be specific about what you searched. "We read the 4 commits touching this line, searched `notes/` and the draft for 'warmup', listed W&B runs from March 3 to 10, and grepped the two Cursor sessions from that week. None states why warmup is 500 steps."

## Research-Specific Traps

- **Justification is not motivation.** Check the date of every piece of evidence against the date of the decision. An ablation run after the choice supports it but did not cause it. Put it under Technical Rationale.
- **The paper's story is post hoc.** Method sections are written after the fact and present choices as principled. The commit and run history records what happened. When they disagree, surface both.
- **Inherited defaults.** Many constants come from a reference implementation, an appendix table, or a library default. "Inherited, never revisited" is a real answer. Look for the origin before inventing a rationale.
- **Agent-authored rationale.** A comment, commit message, or transcript line written by an AI assistant is direct evidence of what the agent said, not of a considered decision. Label it as agent-written. A user message endorsing it is stronger than the agent's claim alone.
- **Weak empirical evidence.** A result cited as the reason may be one seed, a confounded comparison, or tuned on the test set. When citing a run, state the number of seeds, the spread if known, and whether the comparison isolates the change. Report these facts without judging the methodology further.
- **Math as intent.** "This is the standard proximal step" describes the code. It is not evidence that the author chose it for the properties of a proximal step.

## Phrasing Guide

### Words that carry confidence. Use carefully

These imply **Direct** or **Supported** confidence. Don't use them for inferences.

- "because", "the reason is"
- "was chosen to", "was designed to"
- "fixes", "addresses", "ensures"
- "we decided", "the advisor decided"

If you use these, a citation should sit right next to them.

### Words that hedge. Use for inferences

"appears to", "seems to", "likely", "suggests", "is consistent with", "one reading is", "plausibly", "may have been", "the evidence points toward".

### Words to avoid

- "obviously", "clearly", "of course". If it were obvious, the researcher wouldn't be asking.
- "just" (as in "it's just for numerical stability"). Dismissive and usually hides uncertainty.
- "I think" / "I believe". Use "the evidence suggests."
- "provably", "guarantees", "optimal" when describing a choice, unless you cite the result and its assumptions hold here.

### Avoid rationalization

Code or math that "makes sense" today may have been written for reasons that no longer apply, or that were wrong at the time. Don't retrofit a clean rationale onto messy history. Resist the urge to:

- Assume the author did the theoretically right thing and work backward to justify it
- Assume a pattern repeated across scripts was intentional when it may be copy-paste between experiment files
- Turn an absence of evidence into evidence of absence ("no run shows instability, so it was never unstable")

## The Sycophancy Trap

Researchers often phrase `why` questions with a hypothesis built in: "Why do we use Huber here, I assume it's for robustness to outliers?" Don't simply confirm it. Treat it as one candidate and check the evidence independently. If the evidence supports it, say so with citations. If not, say so and present what the evidence does support. This matters more when the researcher wrote the code: memory of past reasoning is itself reconstructive.

## When Evidence Contradicts

Surface both sides with citations. Don't pick the tidier story. A typical research pattern:

- **The draft says** "we use Adam following common practice"
- **The history shows** SGD until two days before the deadline, then a switch right after a diverged run

Both may be partly true. Present both and let the researcher make the call.

## When Evidence Is Missing

An explicit "we don't know" is one of the most valuable outputs. The researcher learns the answer isn't in the obvious places, that they may need to ask a collaborator or advisor, or that the choice was never deliberate and can be revisited freely.

Name each gap concretely: the question, the sources searched, what was searched for in each, and what came back.

## Calibration Check Before Finalizing

Before delivering the output, review every claim and ask:

1. Does it have a citation? If not, add one or move it to Inferred or Competing Hypotheses.
2. Is the phrasing calibrated to the tier? A Direct claim can use "because". An Inferred claim cannot.
3. Is the evidence dated before the decision? If not, move it to Technical Rationale.
4. Am I treating the code or the math as evidence for its own intent? If so, remove or reclassify.
5. Is any agent-written rationale labeled as such?
6. Is there a "What We Don't Know" section? If no gaps are listed, be suspicious.
