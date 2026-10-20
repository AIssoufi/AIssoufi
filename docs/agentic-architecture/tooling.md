# Tooling

| Tool | Role in the fleet |
| --- | --- |
| [Claude Code](https://claude.com/claude-code) | Runs every agent: an interactive session per super master, master and worker, and sub-agents launched from them |
| [cmux](https://github.com/manaflow-ai/cmux) | A terminal with one workspace per agent, messages between agents, and a sidebar that reads like a board |
| [Worktrunk](https://github.com/max-sixty/worktrunk) | Creates and removes the Git worktree of each ticket, and runs hooks around it |
| [GitHub CLI](https://cli.github.com) | Issues, the Status and Priority fields of each repository's project, pull requests, checks and merges |
| [Cloudflare](https://www.cloudflare.com) | Hosts the projects on Pages or Workers, with a preview URL for each pull request |
| Playwright and Chrome | Screenshots at four widths, and the checks that need interaction |

## Claude Code

- A session is started with its charter appended to its system prompt, a name (`--name ISF-35`), an effort (`--effort high`), the auto permission mode, and its identity in its environment.
- **Skills** in `~/.claude/skills` hold the conventions and the procedures, and load when a task matches their description. **Rules** in `~/.claude/rules` are short reminders, scoped to file paths, that point to a skill. **Agents** in `~/.claude/agents` define each sub-agent's instructions, tools, model and effort. **Hooks** run checks that need no judgment.
- Each master schedules its own checkpoint as a recurring task every 30 minutes.

## cmux

- `cmux agent message <workspace> --from <name> "<text>"` delivers a message into another agent's session.
- `ticket.mjs sync` titles each workspace after its branch and files it in the group of its ticket status.
- Two zsh hooks resume a Claude session after a cmux restart, with the options it was launched with, and keep a workspace titled after its branch.
- The owner can open any workspace and talk to its agent directly.

## Worktrunk

- `wt switch --create <ref> --base origin/<base>` creates a worktree next to the repository; `wt remove` deletes it with its merged branch.
- User hooks open the cmux workspace after a start and close it after a removal; project hooks prepare the worktree, for example by installing its dependencies and giving it its own port for the dev server.

## Scripts of the setup

| Script | Does |
| --- | --- |
| `ticket.mjs` | `list`, `start` (with `--agent`), `status`, `sync`, `cleanup` and `key` of the tickets |
| `fleet.mjs` | the registry, the journal, and `start-master`: see [Traceability](traceability.md) |
| `usage.mjs` | the quota steward's reading: see [Capacity and quota](capacity-and-quota.md) |
| `screenshots.mjs` | every route at every width in one Playwright run, for the visual tester |

They are Node.js scripts that keep their logic in pure helpers, tested with `node --test`, and call `git`, `gh`, `wt` and `cmux` from thin I/O modules.

## GitHub

Each repository has an autolink that turns its ticket key (`ISF-35`) into a link to the issue, and a GitHub Project whose Status field drives the cmux board. Merges use a merge commit whose body is not the pull request's conventional title, so a changelog tool does not list a change twice.
