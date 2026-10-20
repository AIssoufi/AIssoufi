# Tooling: GitHub

The [GitHub CLI](https://cli.github.com) (`gh`) 2.102.0 is how the agents and the scripts read and write GitHub: issues, project fields through the GraphQL API, pull requests, checks and merges.

## Projects: status and priority

- Each repository has its own GitHub Project, with a *Status* field and a *Priority* field from P0 to P3.
- P0 is a broken build, a site down or an exposed secret, P3 is polish. A master starts the tickets by priority, and only P0 and P1 when the quota is in its `save` mode.
- `ticket.mjs` sets the status, *In Progress* at the start, and `sync` reads it to file the cmux workspaces: the sidebar follows the board.

## Autolinks for ticket keys

Each repository has an autolink from its key to its issues: `ISF-` on issoufi.fr, `CHESS-` on chess, `C4-` on connect-four, for example. `ISF-35` in a commit, a pull request or a comment becomes a link to issue 35.

The scripts read the key from the autolink (`gh api repos/<owner>/<repo>/autolinks`), so the branch, the worktree, the workspace and the worker all carry the same name, `ISF-35`. A repository without an autolink is refused, with the `gh api` command that creates it.

## Commits and pull requests

- **Conventional Commits**, such as `feat`, `fix` and `docs`, checked by commitlint (`@commitlint/config-conventional`, 18.6.1 on issoufi.fr) in a Git `commit-msg` hook, and again by the CI on every commit of a pull request. On issoufi.fr, release-please writes the changelog and the version from them.
- **A ticket reference in every commit**: a `Refs: ISF-35` trailer, which the worker's charter requires. It sits in the last paragraph of the message, with `Co-Authored-By`, so that `git interpret-trailers` reads it. The branch, the commits and the pull request, with `Closes #35`, all lead back to the ticket.
- **Merges**: a master merges with `gh pr merge <pr> --merge --match-head-commit <sha> --body "Closes #<issue>"`. `--match-head-commit` refuses the merge when a commit was pushed after the master's check. The body is not the conventional title, so release-please does not list a change twice.
- **Rulesets**: on issoufi.fr, `develop` and `main` refuse direct pushes, force pushes and deletions, so every change goes through a pull request.
