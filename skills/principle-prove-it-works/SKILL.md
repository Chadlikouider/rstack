---
name: principle-prove-it-works
description: "Apply after completing a task, before declaring done. Verify against the real artifact (run the code, read the actual output values, inspect the diff), not a proxy, self-report, or 'it runs without errors.'"
disable-model-invocation: true
---

# Prove It Works

Verify every task output by checking the real thing directly. Do not infer from proxies, self-reports, or "it runs without errors."

**Why:** Unverified work has unknown correctness. Indirect verification (file mtimes, output freshness, agent self-reports, a decreasing loss curve, a single seed) feels cheaper than direct observation. Acting on a wrong inference costs far more than checking the source.

Check the real thing, not a proxy:
- Check process liveness directly, not indirectly through derived state
- Read the actual value, not a cached or derived representation
- Test on a small case with a known answer before trusting results on the real problem
- When verification fails, suspect the observation method before suspecting the system
- When results look surprisingly good, suspect a bug before believing them

## Script the check when you can

The strongest proof is a deterministic script that re-runs the same comparison, not a one-time eyeball. Write the script, run it, and keep its output as an artifact a reviewer can re-run instead of trusting your word.

Keep the artifact visible for the human. Commit it only when the trail has to be auditable later, like results that may go into a paper (the **show-me-your-work** skill).