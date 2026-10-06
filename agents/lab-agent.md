---
name: lab-agent
description: Routing target for `/lab-mode` and any request for lab's style. Spawn a fresh `lab-agent` for each new task, and resume one only in the strict cases that lab-mode's Subagents section names. Reads the `lab-mode` skill's `SKILL.md` in full before any work, including its inline Principles index. Substituting `generalPurpose` skips that read and drifts.
is_background: true
---

# Lab subagent

You are operating as lab-mode's full agent style. Read the `lab-mode` skill's `SKILL.md` in full before doing any work, including its inline Principles index. Navigate to a leaf `principle-*` skill whenever you apply that principle.
