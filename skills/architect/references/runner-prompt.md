# Architect runner prompt

The orchestrator passes this file to every parallel candidate runner during Phase B and fills in the variable inputs around it: the task, the Phase A grounding artifacts, the isolated working directory, and the output path. Each runner works in its own git worktree or subdirectory, so candidates stay independent.

You are producing one candidate design in architect's parallel exploration. Read the **architect** skill in full first. That's the workflow you're inside. Output a candidate design package: evaluation protocol, caller's usage, type sketch, function signatures, module map, and prose rationale shaped per `[rationale-template.md](rationale-template.md)`.

Apply the following discipline. The orchestrator compares candidates on these axes to pick a base.

- Caller's usage first. Write the README-style usage and two or three real call sites before the types, then derive the type sketch from them. The usage is the spec. The two must agree, so reconcile the sketch to the usage, not the reverse.
- Data structures first. Trace each dominant access pattern through the structure, including the path from a finished run to its row in the results table. If the answer is "we'll add a cache later" or "we'll parse it out of the slogs," the structure is wrong.
- Interface depth. Prefer a small interface that pulls complexity into the callee. Keep framework and file-format types (solver handles, dataset dicts) off the public API. Exception: any setting that can change a reported number stays explicit in the config.
- Fair by construction. Method and baselines share data loading, preprocessing, evaluation, and budget enforcement. Only the thing the claim is about differs.
- Shared state: if two actors might both write, default to per-actor state merged at the read boundary, per the **separate-before-serializing-shared-state** principle skill. In a sweep, that means one output directory and one RNG per run.
- Make boundaries visible. `not implemented` bodies, `TODO` pseudocode for tricky logic, doc comments stating intent, invariants, shapes, and units. A reader should trace data from input to output through types and signatures alone.
- Encode invariants in types: hard-to-misuse types > runtime checks > prose comments, per the **encode-lessons-in-structure** principle skill.
- Validate at boundaries, trust types inside, per the **boundary-discipline** principle skill. Method, objective, and metric logic as pure functions.
- Single source of truth per invariant. Derive instead of sync. Results tables derive from logged runs, never from hand-copied numbers.
- Idempotent runs, per the **make-operations-idempotent** principle skill. A preempted and resumed run matches an uninterrupted one. Rerunning a sweep skips finished runs instead of duplicating them.
- Reproducible from the record. Log config, seed, commit, data version, and environment before the first step, freeze the config, and name any remaining nondeterminism.
- Short call chains. If tracing the flow needs more than three files, flatten it, per the **laziness-protocol** and **minimize-reader-load** principle skills.

You are one of several runners, each on a different model. Produce your best design and don't hedge toward a safe middle. Differences between candidates are the signal used to pick a base and graft.