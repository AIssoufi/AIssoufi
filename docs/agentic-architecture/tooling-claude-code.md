# Tooling: Claude Code

[Claude Code](https://claude.com/claude-code) 2.1.303 runs every agent of the fleet. Each long-running agent is an interactive session in its own [cmux](tooling-cmux.md) workspace, so the owner can open it and type into it. Sub-agents run inside a session, launched with the Agent tool.

## How a session is started

A script builds the command line, so every session of a role starts the same way. A worker, started by `ticket.mjs start --agent`:

```bash
claude --settings '{"env":{"BROWSER":"none","CLAUDE_CODE_AUTO_COMPACT_WINDOW":"200000","TICKET_REF":"ISF-35","TICKET_MASTER":"<master workspace>","TICKET_BASE":"develop"}}' \
  --permission-mode auto --effort high --name ISF-35 \
  --append-system-prompt-file ~/.claude/skills/ticket-master/worker.md \
  'Work on ticket ISF-35 (<issue URL>) in this worktree. Follow your worker instructions.'
```

A master, started by `fleet.mjs start-master`, gets the same options with its fleet name (`MASTER-SITE`), its role, its parent and the super master's workspace in its environment, and `master.md` as its charter.

| Option | Why |
| --- | --- |
| `--append-system-prompt-file` | The charter of the role is part of the system prompt, not of the conversation, so a compaction never summarizes it away |
| `--name` | The ticket reference or the fleet name. It titles the session, and the scripts count the running workers by it in the process list |
| `--effort` | Chosen per ticket: `high` by default, `xhigh` for architecture, the build, CI or deployment, `medium` for docs or a dependency update. A master runs at `high` |
| `--permission-mode auto` | The agent works without stopping at each tool call: a classifier approves or refuses each one. It refuses, for example, the option of `ticket.mjs` that bypasses the worker cap |
| `--settings` | The identity of the session, and `BROWSER=none`, so that a dev server never opens a tab for each worker. cmux does not record this option for a resume, so the script also saves the same variables in the workspace |

## Compaction window per role

Claude Code compacts a conversation once its context reaches a window, which `CLAUDE_CODE_AUTO_COMPACT_WINDOW` sets per session. Each turn re-reads the whole context, so a smaller window makes every turn cheaper.

| Role | Window | Why |
| --- | --- | --- |
| Worker | 200,000 tokens | Replayed on a week of tickets, it saved 13.5% of the workers' input tokens against Claude Code's default of about 267,000, for twice as many compactions of about 90 seconds each |
| Master | 150,000 tokens | A master coordinates rather than reads code, and keeps what it must not forget in its state file |
| Super master | Claude Code's default | No override in the setup |

## Skills, rules, sub-agents and hooks

All of them live in `~/.claude`, outside the repositories, so every agent of every repository shares them.

- **Skills**: 21 in `~/.claude/skills`. Each holds one convention or procedure, and loads only when a task matches its description. Some carry their scripts, such as `ticket-worktree` and `agent-fleet`.
- **Rules**: 11 short files in `~/.claude/rules`, 165 lines in all. Each is a reminder, some scoped to file paths, that names the skill to follow.
- **Sub-agents**: 7 definitions in `~/.claude/agents`, each with its tools, model and effort in its front matter: Opus at `high` for the conventions reviewer, the auditor, the observer, the gardener and the analyst, Sonnet at `medium` for the visual tester and the quota steward. A sub-agent starts from a written brief, without the conversation. See [Roles](roles.md#sub-agents).
- **Hooks**: a `PreToolUse` hook on Bash refuses a command that gates its next step on `tail`, `head` or `grep` rather than on the check piped into them. In a cmux terminal, `claude` resolves to cmux's wrapper, which adds cmux's own hooks: they deliver the messages between agents and record each session so it can be resumed.
- **Scheduled tasks**: each master and the super master schedule their own checkpoint as a recurring task every 30 minutes.

## Why Claude Code

- Conventions, procedures, sub-agents and checks are plain files, shared by every session and changed in one place.
- Each sub-agent has its own model and effort, so an observation task does not pay for deep reasoning.
- A session can be started from a script with its charter, its name, its effort and its environment, then resumed with the same options.
