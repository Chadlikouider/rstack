---
name: why
description: "Use for 'why is this implemented this way', 'why this loss / step size / relaxation / assumption', 'where did this number come from', 'why did we drop this baseline', or recovering the reasoning behind past research work. Searches each evidence source (code history, notes and drafts, experiment records, literature, conversations, past agent sessions) in parallel, then returns a cited, confidence-tiered read that keeps the recorded rationale apart from the technical justification. Use how for what the code does."
disable-model-invocation: true
---

# Why

Recover the reasoning behind a research decision: a piece of code, an algorithmic or mathematical choice, an assumption, a constant, or an experimental setup.

Companion to the `how` skill. `how` answers what the code does and how it works. `why` answers what led to its shape: the paper it follows, the run that failed, the reviewer who asked, the derivation that needed an assumption, or nothing at all.

Research code has two kinds of why. Keep them apart:

- **Recorded rationale.** Why it was actually chosen, as someone wrote it down at the time (a commit, a note, a run, a message). This is history.
- **Technical rationale.** What theory, literature, or results say about the choice, whether or not anyone thought of it then. This is justification.

Both are useful. Confusing them is the main failure mode: a clean theorem found today is not evidence that it was the reason.

Each spawn below names a role line in the `rstack-models.mdc` rule and a default. Set `model` to that line's value, or to the default if the rule or the line is missing. Leave `model` unset when the value is `auto` or `inherit-parent`. If the Task tool rejects a slug, use the default and say so. If it rejects the default, use the closest valid slug of the same family from its error message.

## Operating Posture

Operate as a **careful, cautious, and precise investigator**. Be clear about what you know vs what you're inferring. Read `references/epistemics.md` for the confidence framework and phrasing guide. The synthesizer must follow it.

## Step 1. Understand the Target and the Question

The **target** is usually one of:

- **Code**: a function, a module, a pattern, a numerical trick
- **A number**: a learning rate, a big-M, a tolerance, a time limit, a seed, a split ratio
- **A mathematical choice**: a loss, a relaxation, a regularizer, an estimator, an approximation, a step-size rule
- **An assumption**: convexity, smoothness, i.i.d. data, a known constant, a simplification in the model
- **An experiment**: a baseline kept or dropped, a dataset split, a metric, an ablation, a result that changed

The **question** is usually a design rationale, a choice among alternatives, the origin of a number, whether it was inherited from a paper or reference implementation, what failure motivated it, whether the reason still holds, or a broad "how did this come to be" sweep.

If the target is vague, make your best guess from context (open files, recent edits, the draft or experiment being discussed). State your interpretation briefly so the user can redirect, then proceed.

## Step 2. Establish the Anchor

Before spawning investigators, pin the target to concrete artifacts. You need:

- The file path(s), line range(s), and key symbols
- The research object it maps to, if any: a config key, an equation or section in the draft, a run ID, a figure or table
- The commits that introduced and changed it, **with dates**
- A **time window** around those dates. Most research evidence (runs, notes, messages, agent sessions) is found by time, not by link.
- Explicit pointers already present: cited papers, arXiv IDs, DOIs, run IDs, issue numbers

Build this inline.

```bash
# Last-touch commits for the target lines
git blame -L <start>,<end> <file>

# File history with dates, through renames
git log --follow --format='%h %ad %s' --date=short -- <file>

# When a constant or expression first appeared (pickaxe), across branches
git log --all -S '<exact_string>' --format='%h %ad %s' --date=short

# Explicit pointers near the target
rg -n -C3 -i '(arxiv|doi|et al|theorem|lemma|eq\.|see |following|todo|fixme|hack|note)' <file>

# Where else the symbol or value appears (configs, notebooks, drafts, notes)
rg -n '<symbol_or_value>' --glob '*.{py,yaml,yml,json,toml,ipynb,tex,md}'
```

If the history is thin (no git, or a trail of `wip` commits), look for sibling copies of the project (`proj_v1/`, `proj_v2/`, `old_proj/`) and diff them. Researchers often copy folders instead of branching, and the diff between two copies may be the only change record.

Pull PRs with `gh` only if the repo actually uses them.

Capture this as seed context and pass it to the investigators.

## Step 3. Spawn Parallel Investigators (default posture)

**Default to the full parallel investigation.**

### Discovery

List what is searchable before spawning anything. Probe the workspace:

```bash
git rev-parse --is-inside-work-tree && git log --oneline | wc -l
ls -d notes* docs* paper* draft* results* outputs* logs* runs* wandb mlruns multirun lightning_logs 2>/dev/null
rg --files -g '*.{ipynb,tex,bib,pdf,md}' | head -50
```

Then list the available MCPs (the available-tools map, or the `mcps/` directory Cursor exposes) and map each one to a category below. Check for past agent sessions (see `references/sources.md`).

Aim for a complete **coverage map**, not a minimal one. Document the null, don't skip the search.

Launch all matching investigators in a single message so they run concurrently. One investigator per category. Don't ask one agent to cover several.

Subagent config (each):
- `subagent_type`: `generalPurpose`
- `model`: the `why investigators` line, default `grok-4.7-xhigh-fast`
- `readonly`: `false` (agent mode). **Do not use readonly/Ask mode.** It strips MCP access, which disables MCP-backed investigators. Investigators still shouldn't write anything.

Each investigator gets:
1. The base prompt from `references/investigator-prompt.md`
2. Its category section from `references/sources.md`
3. The **Failed-run forensics** section of `references/sources.md` **if the target looks defensive or numerical** (epsilons, clipping, clamps, NaN guards, warmup, restarts, fixed seeds, solver tolerances, big-M values, time limits, fallbacks)
4. The **Origin of a number** section of `references/sources.md` if the question is about a constant or hyperparameter
5. The anchor from Step 2
6. The user's original question

### Investigator roster. One per available evidence category

Each entry names the category and the kind of "why" it uniquely surfaces.

1. **Code history** (git, `gh`, sibling version folders). Always spawn. Best at surfacing *when things changed, in what order, and what the author said at commit time*.

2. **Notes and drafts** (in-repo notes, READMEs, notebook markdown, LaTeX drafts, derivations; Notion, Obsidian, Google Docs, Overleaf MCPs). Best at surfacing *the written argument*: derivations, alternatives considered, the story the paper tells.

3. **Experiment records** (local logs, `outputs/`, sweep configs, result tables, notebook outputs; W&B, MLflow, TensorBoard MCPs). Best at surfacing *the empirical why*: what was tried, what failed, what the numbers were when the decision was made.

4. **Literature** (citations in code and drafts, `.bib`, PDFs in the repo, reference implementations, library defaults; arXiv, Semantic Scholar, Zotero, web search). Best at surfacing *the inherited or theoretical why*: the paper it follows, the theorem it needs, the default it copied.

5. **Conversations** (Slack, Discord, email, GitHub issues, OpenReview reviews and rebuttals). Best at surfacing *the social forcing function*: an advisor's request, a collaborator's suggestion, a reviewer's demand.

6. **Past agent sessions** (Cursor agent transcripts and other agent logs). Best at surfacing *the reasoning trail of earlier AI-assisted work*, including choices an agent made on its own without discussion.

Code history, notes and drafts, and literature are almost always searchable through local files and the web.

### When to skip an investigator

Only skip with an **explicit, written justification** that goes in the final "Sources Consulted" section. Two valid reasons:

- **The source is not accessible** here. Flag this as a gap, not a choice. Example: "Conversations skipped. No chat or email MCP available, so advisor discussions were not searchable."
- **The source is provably irrelevant**, not just "probably irrelevant." Example: "Experiment records skipped. The target is a proof-checking script that is never part of a run."

If the question is narrow and the answer is written explicitly in one place (a comment citing the exact paper and equation, a commit message stating the reason), you may answer inline **only after** confirming the other categories would not change the answer. Say which ones you skipped and why. Keep recorded and technical rationale apart even then.

## Step 4. Synthesize

Spawn one synthesizer subagent:

- `subagent_type`: `generalPurpose`
- `model`: the `why synthesizer` line, default `claude-opus-5-5-max`
- `readonly`: `false` (agent mode). The synthesizer spot-verifies citations and may check a cited theorem or reference implementation, which can require MCP or web access.

The synthesizer gets:
1. The investigator findings, including null results and skipped categories with their justification
2. The anchor from Step 2
3. The user's original question
4. The epistemics framework from `references/epistemics.md`
5. The synthesizer prompt template from `references/synthesizer-prompt.md`

## Step 5. Present

Present the synthesizer's output to the user. You may lightly edit for clarity or add context from the conversation, but **do not rewrite the confidence language** and do not merge the technical rationale into the recorded rationale.

## Output Format

The structure is the one in `references/synthesizer-prompt.md`: The Question, The Anchor, What We Found, What We Can Reasonably Infer, Competing Hypotheses, Technical Rationale, What We Don't Know, Sources Consulted, Confidence Summary. Adapt as needed, but keep the confidence separation intact, and keep Sources Consulted as one line per investigator, including the ones that returned nothing or were skipped, with the reason.

After Sources Consulted, if the `why` question is a precursor to changing the code or the experiments, convert the findings into a **Preserve / Change / Avoid / Risk** constraint set for planning the change. In research the main Risk is usually reproducibility: which reported numbers, figures, or claims depend on the current behavior, and which runs would have to be redone.

## Common Failure Modes to Avoid

- **Recency bias.** Assuming the most recent commit is authoritative. The current shape is often the accretion of many earlier decisions. Trace back.
- **Justification passed off as motivation.** A later ablation or a theorem found today explains why a choice is sound, not why it was made. Check dates.
- **Trusting the paper's story.** Method sections are written after the fact and tidy up choices made for convenience. When the draft and the commit or run history disagree, surface both.
- **Inventing a rationale for an inherited default.** Many constants come unchanged from a reference implementation, a paper's appendix, or a library default. "Inherited, never revisited" is a common and useful answer. Check for it before building a theory.
- **Taking agent-written rationale at face value.** Comments, commit messages, and transcripts written by an AI assistant may state a reason nobody checked. Cite them as what the agent said, not as the researcher's decision.

## Reference Files

- `references/epistemics.md`. Confidence tiers, recorded vs technical rationale, and research-specific traps. The synthesizer must follow it.
- `references/investigator-prompt.md`. Base prompt template for investigator subagents.
- `references/sources.md`. One section per evidence category, plus the cross-cutting Failed-run forensics and Origin of a number sections. Give each investigator only the sections it needs.
- `references/synthesizer-prompt.md`. Prompt template for the synthesizer subagent, including the output format.
