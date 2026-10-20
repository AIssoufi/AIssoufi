# Tooling: cmux

[cmux](https://github.com/manaflow-ai/cmux) 0.65.2 is a macOS terminal for coding agents. Every agent of the fleet runs in its own cmux workspace, and the scripts drive cmux through its command line.

## One workspace per agent

- `cmux workspace create --name ISF-35 --cwd <worktree> --command '<claude command>' --env KEY=value` opens a workspace without focusing it, and starts the agent in its first terminal. cmux keeps the environment with the workspace and gives it to every shell it starts there, after a restart too.
- A workspace is titled after its agent: `SUPER-MASTER`, `MASTER-SITE`, or the ticket of a worker, `ISF-35`, which is also its branch. A zsh hook renames it at each prompt where the branch changed.
- `cmux workspace close` refuses to close a workspace whose processes would be killed, which protects a running agent.
- The owner can open any workspace and talk to its agent directly.

## Messages between agents

```bash
cmux agent message <master workspace> --from ISF-35 "DONE: <pull request, review verdict>"
```

cmux delivers the text through the recipient's hooks, never by typing into its terminal, so it cannot land in a half-typed prompt. An idle session wakes up to read it, and a busy one reads it at its next step. Each message starts with a keyword, such as `FINDING`, `QUESTION` or `DONE`: see [Roles](roles.md#messages).

## The sidebar as a board

`ticket.mjs sync` reads the branch of every workspace, the ticket of that branch and its status in the repository's GitHub Project. Then it:

- gives each status one pinned, colored group, kept even when empty, with the same color in every window;
- files each ticket workspace in the group of its status, and titles it after its branch;
- creates each group with an idempotency key, so two syncs running at once never create it twice.

The scripts call it after each start, status change and cleanup, so the sidebar shows what is in progress, in review or blocked across all repositories.

## Driving a terminal: send, send-key, read-screen

Two scripts drive a Claude Code session the way a person would, and never kill a process:

- **Ending a worker.** `ticket.mjs cleanup` finds the terminal of the session by its terminal device (`cmux tree --json`), types `/exit` with `cmux send` and Enter with `cmux send-key`, and closes an open question with Escape first. When the session still has background tasks, it reads the screen with `cmux read-screen` and confirms the exit. It waits up to 30 seconds, so the session saves its state.
- **Reading the quota.** `usage.mjs` types `/usage` into `USAGE-PROBE`, an idle Claude Code session kept open in its own workspace, reads the panel with `cmux read-screen`, and closes it with Escape. A reading spends no token and reads no credential: see [Capacity and quota](capacity-and-quota.md).

## Resuming after a restart

cmux records the Claude Code session of each terminal with its launch options (`cmux surface resume show`). cmux 0.65 does not resume an agent that was idle when it quit, so its own automatic resume is turned off (`terminal.autoResumeAgentSessions: false`). A zsh hook resumes the session instead, at the first prompt of a restored terminal, through `ticket.mjs resume-command`: it comes back with its effort, its permission mode and its charter. A session ended with `/exit` stays stopped, so a restart never brings back more workers than the cap allows.

## Why cmux

- Messages go through the agents' hooks, not through their keyboard.
- What the fleet needs is scriptable: workspaces, groups, terminals, keys and screens.
- The owner sees every agent in one window, and can step into any of them.
