# Roles

## Long-running agents

Each is an interactive Claude Code session in its own cmux workspace, with its charter appended to its system prompt.

| Agent | Name | Started by | Effort |
| --- | --- | --- | --- |
| Super master | `SUPER-MASTER` | the owner | |
| Master | `MASTER-<REPO>`, such as `MASTER-SITE` or `MASTER-CHESS` | the super master, `fleet.mjs start-master` | `high` by default |
| Worker | the ticket reference, such as `ISF-35` | its master, `ticket.mjs start --agent` | chosen per ticket |

A worker's effort follows what its ticket asks, not its size in lines: `xhigh` for architecture, build, CI or deployment; `high` for a feature or a fix a visitor sees; `medium` for a mechanical change, docs or a dependency update. A worker that finds its ticket harder says so, and its master restarts it with more effort in the same worktree.

A master runs with a smaller context window than a worker (compaction at 200,000 tokens). It coordinates rather than reads code, and its state file holds what it must not forget, so a smaller window keeps each of its many turns cheaper without losing anything.

## Sub-agents

Launched with the Agent tool and a written brief. Each has its own model and effort, chosen for the job, so that observation tasks do not pay for deep reasoning.

| Sub-agent | Launched by | Job | Model, effort |
| --- | --- | --- | --- |
| `conventions-reviewer` | a worker | the diff against every skill, rule and project `CLAUDE.md`, plus lint, types and tests. Read-only | Opus, high |
| `visual-tester` | a worker | the change in a browser at four widths, and the regressions around it | Sonnet, medium |
| `repo-auditor` | a master | bugs, outdated or vulnerable dependencies, missing CI, deployment, secrets, accessibility, README gaps. Read-only | Opus, high |
| `workflow-observer` | a master, or the super master for the fleet | up to five evidenced improvements of the workflow. Read-only | Opus, high |
| `quota-steward` | the super master | the worker capacity from the quota and the machine. Being set up | |
| `fleet-analyst` | the super master | the efficiency metrics of the masters. Being set up | |

## Messages

Agents talk through `cmux agent message`, which lands in the recipient's session. Every message starts with a keyword, so the recipient knows what to do with it without the sender's context.

| From | To | Keywords |
| --- | --- | --- |
| Worker | master | `FINDING`, `QUESTION`, `BLOCKED`, `UPDATE`, `DONE` |
| Master | super master | `QUESTION`, `REQUEST`, `PROPOSAL`, `REPORT`, `UPDATE`, `BLOCKED` |

Routine progress is never sent: it fills the recipient's context and calls for no action. Messages are in the owner's language; code, docs, commits, issues and pull requests stay in English.
