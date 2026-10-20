# Tooling: Worktrunk

[Worktrunk](https://github.com/max-sixty/worktrunk) (`wt`) 0.80.0 manages Git worktrees: one directory per branch, each with its own files, dependencies, build output and dev server port. [Life of a ticket](ticket-lifecycle.md#why-one-worktree-per-ticket) gives the reasons for one worktree per ticket.

## One worktree per ticket

- The branch is named after the ticket reference, such as `ISF-35`, and starts from the base branch on the remote. `ticket.mjs start` runs `wt switch --create ISF-35 --base origin/develop --no-cd`.
- The worktree is named `<repo>.<branch>`, such as `chess.CHESS-15`.
- `wt remove <branch> --yes --foreground` deletes the worktree and its merged branch. `ticket.mjs cleanup` runs it only for a closed ticket whose worktree holds no uncommitted change and no commit missing from the base.

## User config

`~/.config/worktrunk/config.toml` applies to every repository:

```toml
worktree-path = "~/Sources/worktrees.noindex/{{ repo }}.{{ branch | sanitize }}"

[post-start]
cmux = "node ~/.claude/skills/ticket-worktree/scripts/ticket.mjs open '{{ worktree_path }}'"

[post-remove]
cmux = "node ~/.claude/skills/ticket-worktree/scripts/ticket.mjs close '{{ worktree_path }}'"
```

- **`worktree-path`** puts every worktree in one folder whose name ends in `.noindex`, which Spotlight skips. Spotlight indexed the `node_modules` of each new worktree, and that took up to 3 cores of the machine. Without this line, Worktrunk puts a worktree next to its repository.
- **`post-start`** opens a cmux workspace on the new worktree, titled after its branch and filed in the group of its ticket status. `ticket.mjs start` opens the workspace itself, with the worker in it, so it sets an environment variable that makes this hook stand down.
- **`post-remove`** closes the cmux workspaces of the removed worktree. cmux keeps one whose processes still run.

## Project hooks

Each repository can add its own hooks in `.config/wt.toml`. Those of issoufi.fr prepare a new worktree before anything else runs in it, both at once:

```toml
[pre-start]
install = "pnpm install --frozen-lockfile"
port = "echo PORT={{ branch | hash_port }} > .env.local"
```

- **`install`**: a new worktree has no dependencies yet. The frozen lockfile installs the versions it records, and fails rather than change them.
- **`port`**: `hash_port` turns the branch into a port from 10000 to 19999, the same for the branch every time, written to `.env.local`. The dev server, the preview server and the browser tests read it, and a taken port stops the server rather than moving it to another port. Several worktrees serve the app at once without clashing.

## Why Worktrunk

- Worktrees as one command each way, with hooks before and after a start and a removal.
- Templates for the path and the port, so the conventions live in one config file rather than in each script.
