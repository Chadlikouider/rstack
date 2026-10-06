# Evidence Sources

One section per evidence category. Give each investigator only its own section, plus the cross-cutting sections at the end when they apply. The tools named are examples. Adapt to whatever is available.

## 1. Code History

**Contains.** Commits (messages, diffs, timestamps), branches, tags (often marking submissions: `icml-submit`, `camera-ready`), stashes, PRs and reviews if the repo uses them, co-changed files, and sibling copies of the project (`proj_v1/`, `proj_v2/`, `old_proj/`).

**How to search.**

```bash
git log --follow -p -- <file>
git log --all -S '<string>' --format='%h %ad %an %s' --date=iso
git log --all --oneline --decorate --graph -- <file> | head -50
git tag --contains <commit>          # first submission that included it
git stash list
git diff --no-index <proj_v1>/<file> <proj_v2>/<file>
```

**Good evidence.** A message stating a reason. A revert followed by a re-apply. A change landing just before a submission tag. The same constant changed several times in a short window (a tuning trail).

**Pitfalls.**
- Terse messages ("fix", "wip", "try"). Read the diff and use the timestamp to correlate with runs and notes.
- A bulk "initial commit" of code developed elsewhere. Look for the earlier copy.
- Commit messages written by an agent. Flag them.
- Copy-paste between experiment scripts. Find where the pattern first appeared and investigate that commit.

**Return.** Hash, date, author, quoted message, what changed, direct or circumstantial.

## 2. Notes and Drafts

**Contains.** READMEs, `notes/`, TODO files, docstrings with derivations, notebook markdown cells, LaTeX drafts (method, appendix, footnotes, `\todo{}`, commented-out `%` paragraphs), advisor meeting notes, and pages in Notion, Obsidian, Google Docs, or Overleaf.

**How to search.**

```bash
rg -n -i '<symbol|concept|value>' --glob '*.{md,tex,txt,org,ipynb}'
rg -n '^\s*%' <draft>.tex | rg -i '<concept>'    # commented-out text
git log -p -- <draft>.tex | rg -i -C3 '<concept>' # deleted paragraphs
```

Deleted and commented-out paragraphs often hold the alternative that was dropped. For notebooks, search both markdown and code cells.

**Good evidence.** "We use X instead of Y because..." An "alternatives tried" list. A derivation that needs the assumption. A meeting note recording a decision.

**Pitfalls.**
- The draft is a post-hoc narrative. Cross-check against code history and runs.
- Notes describe a plan that later changed. Check dates.
- Several drafts or versions. Find the one closest in time to the decision.
- Overleaf or cloud docs not in git. If not accessible, name the gap.

**Return.** Document path or URL, date, section, verbatim quote, draft or final.

## 3. Experiment Records

**Contains.** Run logs, metrics, per-run resolved configs (for example `outputs/<date>/<time>/.hydra/config.yaml`), sweep definitions and results, result CSVs and tables, plots, notebook outputs, solver logs (Gurobi, CPLEX), and W&B, MLflow, or TensorBoard runs with their notes, tags, and groups.

**How to search.**

```bash
ls -lt outputs/ runs/ logs/ wandb/ mlruns/ multirun/ 2>/dev/null | head -30
rg -l '<param_name>' outputs/ multirun/ --glob '*.yaml'
rg -n -i '(nan|inf|diverg|infeasible|unbounded|time limit|out of memory)' logs/
```

Find runs in the time window around the anchor commit. Diff the configs of runs just before and just after the change. Look for sweeps over the parameter in question. With a tracking MCP, filter runs by date and config value and read run notes.

**Good evidence.** A sweep where the chosen value won. A failed run right before a guard was added. A run note stating the decision. Before and after runs that differ only in the change.

**Pitfalls.**
- One seed, no spread. Report the number of seeds.
- Confounded comparisons: several things changed between two runs.
- Deleted failed runs. What survives is biased toward what worked.
- Runs after the decision justify it but did not motivate it.

**Return.** Run IDs or paths, dates, the config diff, metric values with number of seeds, before or after the decision.

## 4. Literature

**Contains.** Papers cited in comments, docstrings, READMEs, drafts, and `.bib` files. PDFs in the repo. The official code of the method being implemented. Library documentation and defaults. Textbook results.

**How to search.**
1. Follow explicit citations first. Open the exact section, equation, algorithm box, or appendix hyperparameter table.
2. Identify the canonical source of the method if none is cited (arXiv, Semantic Scholar, Zotero, web search on the algorithm name and key symbols).
3. Compare the reference implementation's structure and constants with the target, line by line where it matters.
4. Check the library default for the constant, in the version the project uses.

**Good evidence.** A comment citing "Eq. (7) of Smith et al. 2021" and Eq. (7) matches. The appendix table lists the same value. The reference repo has the same line. A theorem whose assumptions require the choice (for example a step size at most 1/L).

**Pitfalls.**
- A citation does not mean a faithful implementation. Deviations from the paper are often the most interesting finding.
- Paper and official code often disagree. Record both.
- A paper explains the method, not why this project adopted it.
- Version drift: arXiv v1 vs v3, or a library default that changed between versions.

**Return.** Full reference (authors, year, title, arXiv ID or DOI), exact location, quote, match or deviation, and whether the project's own artifacts cite it or you found it independently. Independently found material is technical rationale only.

## 5. Conversations

**Contains.** Slack, Discord, or Teams threads, email, GitHub issues, OpenReview reviews, meta-reviews and rebuttals, workshop feedback.

**How to search.** Messages in the time window around the anchor. Keywords for the concept and the value. Messages from the advisor or collaborators. In reviews, look for requests for ablations, baselines, assumptions, or clarifications, then match them to commits dated shortly after. Fetch whole threads, not single messages.

**Good evidence.** "Use the dual here, the primal is too slow at this scale." A reviewer asking for baseline X, followed by the commit adding X. A rebuttal promising a change.

**Pitfalls.**
- Brainstorming is not a decision. Look for the considered message.
- Most research decisions happen in meetings and DMs. If they aren't searchable, name the gap.
- If the MCP isn't authenticated, stop and report it. Don't make up findings.

**Return.** Channel or venue, permalink, participants, date, verbatim quotes with attribution.

## 6. Past Agent Sessions

**Contains.** Cursor agent transcripts at `~/.cursor/projects/<slug>/agent-transcripts/<uuid>/<uuid>.jsonl`, where `<slug>` is the workspace path with the leading slash dropped and each `/` turned into `-`. Every line is one chat message. Other agent tools keep similar logs (for example under `~/.codex/`).

**How to search.** Order sessions by real modification time (`ls -t`). Grep for the symbol, value, or concept first, then read only matching regions. Find the session whose edits introduced the target by matching its time to the commit. Only read the current workspace's transcripts unless the user asks otherwise.

**Good evidence.** A user message stating the reason. A discussion where alternatives were compared. An agent proposal followed by an explicit user endorsement.

**Pitfalls.**
- An agent's reason is not the researcher's decision. "Adding `eps=1e-6` for numerical stability" from the agent is what the agent said.
- A user accepting an edit is not the same as endorsing its rationale.
- Often the session that introduced the change contains no reason at all. That is a finding: "introduced by an agent in session X without discussion."

**Return.** Session UUID, date, speaker (user or agent), verbatim quote, whether the user explicitly endorsed it.

---

## Cross-Cutting: Failed-Run Forensics

Not a separate source. Use it when the target looks defensive or numerical: an epsilon in a log or a division, gradient clipping, clamping, NaN or inf checks, warmup, restarts, fixed seeds, determinism flags, solver tolerances, big-M values, time limits, or a fallback to a simpler method. Hunt for the failure that motivated it inside your own source:

- **Code history**: "fix nan", "stabilize", "revert", or a guard added right after a change to the loss, model, or formulation
- **Notes and drafts**: "loss explodes when", "solver stalls on", "infeasible for"
- **Experiment records**: crashed, diverged, or infeasible runs just before the guard, and whether they recovered after
- **Conversations and agent sessions**: the pasted error message or traceback
- **Literature**: a known instability of the method and the standard remedy

The failure, the guard, and a healthy run afterward form the strongest chain. If no failure turns up, "added by habit or copied" stays a live hypothesis.

## Cross-Cutting: Origin of a Number

Use it when the question is about a constant or hyperparameter. Check these origins, and report which ones you ruled out:

1. **Paper**: the value appears in the cited paper's text, appendix table, or official code
2. **Library default**: the value equals the default of the function it's passed to
3. **Tuning**: a sweep or a sequence of runs ends at this value
4. **Derivation**: the value follows from a bound or formula (for example 1/L, or a big-M computed from variable bounds)
5. **Constraint**: hardware, time budget, or dataset size forced it
6. **Guess**: a round number with no trail. A legitimate answer when the other five come up empty.
