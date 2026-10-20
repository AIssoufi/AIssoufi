# Tooling: scripts of the setup

Node.js scripts, kept in the skills of `~/.claude`, glue the tools together. They keep their logic in pure helpers, tested with `node --test` (424 tests on 2026-10-20), and call `git`, `gh`, `wt` and `cmux` from thin I/O modules. They are typed with JSDoc and `// @ts-check`.

## `ticket.mjs`: tickets, worktrees and workspaces

In `~/.claude/skills/ticket-worktree/scripts/`. A ticket is given as `ISF-35`, `#35`, `35` or its issue URL.

| Command | Does |
| --- | --- |
| `list` | the open tickets, with their status, worktree and workspace |
| `start <ticket> [--agent] [--effort <level>]` | the worktree from the base branch (`develop`, or the default branch without it), the status *In Progress*, the cmux workspace, and with `--agent` a Claude Code worker in it |
| `status <ticket> <status>` | sets the project status, then syncs the sidebar |
| `sync` | titles each workspace after its branch and files it in the group of its ticket status |
| `open`, `close` | open or close the workspace of a worktree: the Worktrunk hooks call them |
| `cleanup` | for each worktree of a closed ticket, ends its worker session, then runs `wt remove`. It keeps a worktree with uncommitted changes or with commits missing from the base |
| `resume-command` | prints the command that resumes the Claude Code session of this terminal, with its launch options |
| `key` | prints the ticket key of the repository, read from its GitHub autolink |

**Capacity check.** Before `start --agent`, the script reads `maxWorkers` in `~/.claude/fleet/capacity.json`, which the quota steward writes, or six without the file. It counts the running workers in the process list: Claude Code sessions named after a ticket, such as `--name ISF-35`. At the cap, it refuses and says how to free a slot. The option that bypasses the cap is refused by the auto permission mode.

## Fleet scripts

In `~/.claude/skills/agent-fleet/scripts/`.

| Script | Does |
| --- | --- |
| `fleet.mjs` | The journal. `register` records a session, `log` an action such as `pr.merge AIssoufi/chess#12`, `show` prints who did what, `agents` who was who, and `start-master` starts a master in its own workspace. See [Traceability](traceability.md) |
| `usage.mjs` | The capacity. It reads the plan usage in the `/usage` panel of an idle session, the free memory (`memory_pressure`), the load (`sysctl vm.loadavg`) and the running workers, then writes `capacity.json` with a mode, a cap and the lowest priority to start. See [Capacity and quota](capacity-and-quota.md) |
| `metrics.mjs` | The efficiency, per master over a period (`--since`, `--repo`, `--json`, `--save`): throughput, lead times, quality (failed CI runs, rework, reverts, reopened issues) and token cost. It reads the journal, the session transcripts and GitHub through read-only `gh` requests, and caches the commits of merged pull requests, which never change |

The first report of `metrics.mjs`, for 2026-10-13 to 2026-10-20, counted 150 pull requests merged on issoufi.fr, 131 of them for tickets.

## Review script

`screenshots.mjs`, in the `review-work` skill, takes every route at every width (390, 768, 1280 and 1920 px by default) in one run of the project's own Playwright. It reports sideways scrolling, console errors and failed requests, and saves the screenshots for the visual tester to look at. One command replaces a browser call per page and width.
