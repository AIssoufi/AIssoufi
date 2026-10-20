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
| [Tooling](tooling.md) | Claude Code, cmux, Worktrunk, the GitHub CLI and Cloudflare |

## Status

This page describes the system as it runs today. Parts still being set up are marked as such: the quota steward agent, and the efficiency metrics with their analyst.
