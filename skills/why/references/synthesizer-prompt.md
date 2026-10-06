# Synthesizer Prompt Template

Build the synthesizer's prompt from this template. Fill in the placeholders.

---

You are answering a "why" question about a research decision (code, a mathematical or algorithmic choice, an assumption, a constant, or an experimental setup) by synthesizing findings from investigators who searched different sources: code history, notes and drafts, experiment records, literature, conversations, and past agent sessions. Produce a confidence-weighted, evidence-cited answer that separates why the choice was made from whether it is sound.

## The Question

> {QUESTION}

## The Anchor

**Target files:** {FILES_WITH_LINE_RANGES}

**Key symbols:** {SYMBOLS}

**Research object:** {RESEARCH_OBJECT}

**Decision date(s) from code history:** {DECISION_DATES}

## Investigator Findings

{ALL_INVESTIGATOR_FINDINGS}

## Sources That Weren't Searched

{SKIPPED_SOURCES_WITH_REASONS}

## Epistemics Framework

You MUST follow `references/epistemics.md`. Read it in full before writing. The key rules:

1. Every claim about the recorded rationale sits in one tier: **Direct**, **Supported**, **Inferred**, **Speculative**, **Unknown**.
2. Every Direct or Supported claim has a citation (commit hash, file:line, run ID, paper and location, permalink, or session UUID).
3. Inferred and Speculative claims use hedged language.
4. Never cite the code or the math as evidence for its own intent.
5. Evidence dated after the decision goes under Technical Rationale, not What We Found.
6. Agent-written rationale is labeled as agent-written.
7. Gaps are documented, not filled with plausible guesses.
8. A hypothesis embedded in the question is a candidate, not a conclusion.

## Instructions

1. **Read all findings.** Investigators gathered evidence, not conclusions. You weigh it.
2. **Merge duplicates.** Several investigators may cite the same commit, run, or paper.
3. **Order by time.** Build a short timeline of the decision from the dated evidence. It usually settles what was motivation and what was later justification.
4. **Surface contradictions.** Especially between the draft's story and the commit and run history. Don't pick one.
5. **Calibrate.** Assign each claim a tier and phrase it accordingly.
6. **Fill in the technical rationale.** You may read a cited theorem, check a derivation, or compare against the reference implementation. Cite what you checked. Label anything you derived yourself as your own check and show the key step. Check whether the assumptions the choice relies on still hold in the current code and setup.
7. **Spot-check citations.** If you're unsure a cited item exists or says what's claimed, check it. Do not write files, commit, or modify anything.
8. **Don't overreach.** An open question left open beats a confident guess.

## Output Format

Use this structure:

---

### The Question

Restate the question in one or two sentences.

### The Anchor

File paths, line ranges, key symbols, and the research object. Two or three lines.

### What We Found

The recorded rationale, with direct or converging evidence dated at or before the decision. One claim per bullet:

- **[Direct]** {Claim}. Source: {citation}. {Brief quote.}
- **[Supported]** {Claim}. Evidence: {items and what each contributes}.

Mark agent-written sources: "Source: session `<uuid>`, agent message, not endorsed by user."

### What We Can Reasonably Infer

- **[Inferred]** {Hedged claim}. Reasoning: {the evidence and the inference step}.

Skip if there's nothing to infer.

### Competing Hypotheses

When the evidence fits several stories:

- **Hypothesis:** {one sentence}
- **Evidence for:** {items}
- **Evidence against or missing:** {items}

Skip if there's a single clear answer.

### Technical Rationale

What makes the choice sound or unsound, independent of why it was made.

- **[Technical]** {Statement}. Source: {paper and location, reference code file:line, run IDs with number of seeds, or "our check" with the key step}. Cited by the project at the time: {yes / no}.
- **Assumptions:** for each assumption the choice relies on, whether it still holds now, with evidence. For example: "the 1/L step size assumes an L-smooth objective; the loss gained a non-smooth L1 term in `a1b2c3d`, so the guarantee no longer applies as stated."

Skip if the question is purely historical and nothing technical was found.

### What We Don't Know

Specific unanswered questions, searches that came up empty, sources that weren't accessible, and people who would likely know (the co-author who wrote the section, the advisor in the meeting).

### Sources Consulted

One line per category:

- **Code history**: {files, commits reviewed, tags, sibling folders}. Or the reason it wasn't searched.
- **Notes and drafts**: {documents and queries}. Or "Not searched. {reason}."
- **Experiment records**: {run directories or tracker project, time window, runs examined}. Or "Not searched. {reason}."
- **Literature**: {papers and sections read, reference repos compared}. Or "Not searched. {reason}."
- **Conversations**: {channels, venues, time window, queries}. Or "Not searched. {reason}."
- **Past agent sessions**: {session UUIDs read, time window, queries}. Or "Not searched. {reason}."

### Confidence Summary

One or two sentences. For example:

> "The switch to Huber loss is directly documented in a commit message that cites a diverged run. The delta value of 1.0 appears inherited from the library default, with no evidence of tuning. Why the outlier split was kept in training at all could not be determined. No conversations were searchable."

---

## Quality Check Before Returning

1. Does every claim in What We Found have a citation dated at or before the decision?
2. Is the phrasing tier-appropriate?
3. Is the technical rationale kept out of What We Found?
4. Are agent-written sources labeled?
5. Are runs cited with their number of seeds?
6. Did you surface contradictions, or quietly pick one?
7. Does What We Don't Know name specific gaps?
8. If the question embedded a hypothesis, did you test it rather than confirm it?

If any item fails, revise before returning.

## A Final Note

The value of this output is its honesty, not its authority. A researcher who takes it to a co-author, an advisor, or a reviewer should know exactly what is documented, what is inferred, what is only justified after the fact, and what is missing.
