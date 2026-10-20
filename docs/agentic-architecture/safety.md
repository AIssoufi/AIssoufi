# Safety

Agents run in Claude Code's auto permission mode, so they work without stopping for each tool. What they must never do is written into their charters, and the most important parts are also enforced outside the agents: by GitHub rulesets, by hooks, and by the separation of roles.

## Never

- **Secrets**: never type a password, a token or a payment detail, and never create a secret. A master asks the super master, who asks the owner.
- **Protected branches**: never commit or push to a base branch or to `main`: every change goes through a pull request. Where rulesets protect these branches, as on issoufi.fr, GitHub also refuses direct pushes, force pushes and deletions.
- **Repository settings**: never change a ruleset, a deploy key or a deployment secret. They belong to the owner.
- **Merging one's own work**: a worker never merges its pull request, removes its worktree or closes its workspace. Its master decides.
- **Shared instructions**: only the super master edits the skills, rules and agents, through one skill gardener at a time. Others propose.
- **Other sessions**: never touch another session's workspace or worktree, and never stop a process by its name, since other worktrees run the same tools. A process is stopped by the PID or the port its agent noted.
- **Pushing to a closed pull request**: a worker checks that its pull request is still open, in a guard that blocks the push, right before it.
- **Publishing**: nothing on the owner's GitHub account beyond the issues and pull requests of the agent's repository.
- **Public issues**: no personal data and no vulnerability detail. The auditor marks such findings confidential, and they go to the owner privately.

## Read-only agents

The conventions reviewer, the auditor and the workflow observer change nothing: no edit, no commit, no comment, no message. The auditor works in a throwaway clone; the visual tester builds its comparison in a temporary worktree and cleans up only what it started.

## Waiting for the owner

- A change of layout or design waits for the owner, who sees its before and after screenshots at a phone and a desktop width: a page can meet a ticket's criteria and still look wrong.
- A rewrite in another framework, a new feature or a new design is the owner's decision: a master proposes it with its reason and its cost.
- A release to production goes through the owner, unless the project deploys from its base branch on a schedule.
- A hook or a change of settings is proposed, never applied, by an agent.

## Cloudflare

One browser drives the Cloudflare dashboard, so the super master alone makes the changes there, on a master's request that gives the repository, the build command, the output folder and the subdomain. A worker prepares the repository side only.
