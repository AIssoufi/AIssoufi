# Agentic architecture

How a fleet of [Claude Code](https://claude.com/claude-code) agents plans, implements, reviews and ships the work on my repositories.

The agents, skills, rules, hooks and scripts live in my Claude Code setup (`~/.claude`), which is not public. The repositories hold their work: issues, pull requests, commits, and a short page per project on how agents work on it, such as [issoufi.fr: AI workflow](https://github.com/AIssoufi/issoufi.fr/blob/develop/docs/ai-workflow.md).

## Pages

| Page | What it covers |
| --- | --- |
| [Overview](overview.md) | The hierarchy of super master, masters, workers and sub-agents, and the principles behind it |
| [Roles](roles.md) | What each agent does, what starts it, its model and effort, and how agents talk |
| [Life of a ticket](ticket-lifecycle.md) | From a finding to a merged pull request and a cleaned-up worktree |
| [Reviews and quality](reviews-and-quality.md) | The independent reviewers, the auditor, and the checks before a merge |
| [Traceability](traceability.md) | The journal of who did what, and the registry of who was who |
| [Capacity and quota](capacity-and-quota.md) | How many workers run at once, and how the subscription quota is spent |
| [Continuous improvement](continuous-improvement.md) | Lessons, skills and rules, the workflow observer and the metrics |
| [Safety](safety.md) | What agents never do |
| [Tooling](tooling.md) | The tools of the fleet, their versions, and how they combine on one ticket |
| [Tooling: Claude Code](tooling-claude-code.md) | How a session starts, the compaction window per role, skills, rules, sub-agents and hooks |
| [Tooling: cmux](tooling-cmux.md) | One workspace per agent, messages, the sidebar board, driving a terminal, resuming after a restart |
| [Tooling: Worktrunk](tooling-worktrunk.md) | One worktree per ticket, the user config and the project hooks |
| [Tooling: scripts](tooling-scripts.md) | `ticket.mjs`, `fleet.mjs`, `usage.mjs`, `metrics.mjs` and `screenshots.mjs` |
| [Tooling: GitHub](tooling-github.md) | Projects and priorities, autolinks for ticket keys, commits and merges |
| [Tooling: build, test and deploy](tooling-build-and-deploy.md) | pnpm, Vite, Vitest, Playwright, GitHub Actions and Cloudflare |

## Status

This page describes the system as it runs today. The one part still being set up is marked as such: the efficiency metrics, with their script and their analyst agent.
