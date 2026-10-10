# rstack

> Go deep to go fast.

Agents write code faster than anyone can review it. Most of that code runs. Some of it is wrong in ways that never crash, like a loss averaged over the wrong axis or an eval that reads from the training split. In research, a bug like that changes your results.

**rstack is how I want agents to work.** The agent reads the code before it changes anything. It makes the smallest change that solves the problem. Before it calls the work done, it runs the code and checks the real output.

**The goal is less code that you can trust.** Every extra line is another place for a bug to hide, so rstack pushes agents to delete before they add. Once you can trust one agent's output, you can run several in parallel and spend your time reviewing results instead of watching each one.

**Works with Claude Code, Cursor, and Codex.** Fork it, adapt it to your own lab, and send a PR when you improve something.

## install

```bash
# Claude Code
/plugin marketplace add Chadlikouider/rstack
/plugin install rstack@rstack

# Codex (then install rstack from /plugins)
codex plugin marketplace add Chadlikouider/rstack

# Cursor (then run "Developer: Reload Window")
git clone https://github.com/Chadlikouider/rstack ~/.cursor/plugins/local/rstack
```