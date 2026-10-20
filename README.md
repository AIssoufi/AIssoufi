# ADAM Issoufi

**Frontend architecture, design systems and agentic development.** Paris.

I work on Sophia GO, Sewan's B2B SaaS platform: the UI Kit that fifteen product teams build their screens on, cross-cutting topics, and the subjects my own team owns.

### What I do

- **Own the UI Kit**: 118 React/TypeScript packages published to a private registry, consumed by 15 product teams and by 47 of our 49 applications.
- **Design composable component APIs**: tables (11 packages, 15 features including virtualisation) and filters (13 packages), each followed by migrating every consuming screen. Each brick is versioned on its own, so a breaking change only reaches the teams that use it.
- **Modernise toolchains without breaking anyone**: full TypeScript migration (771 files), npm → pnpm, Node 16 → 22, Jest → Vitest, Webpack → tsup, Storybook 5.3 → 9, Lerna 4 → 8, GitLab CI rebuild. Median merge request pipeline went from 17 min 24 s to 9 min, measured across 2,600 jobs.
- **Develop with AI agents**: frontier models driven from the most advanced agentic harnesses. I continuously maintain specialised skills, rules and sub-agents, to get good code and not just code that works. Each sub-agent is tuned in model and effort to avoid high inference costs with no gain. With git worktrees, I run three or four subjects in parallel.

### How I work with AI agents

My side projects are built by a fleet of Claude Code agents: a super master that coordinates, one master per repository that keeps its board and merges, and one worker per ticket in its own Git worktree. Independent sub-agents review the conventions and test the result in a browser before every pull request, a journal records who did what, and a recurring mistake becomes a skill, a rule or a hook. The [agentic architecture](https://github.com/AIssoufi/AIssoufi/tree/main/docs/agentic-architecture) pages describe how it works.

### Stack

React · TypeScript · styled-components · Storybook · Vitest · Vite · tsup · pnpm · Lerna · monorepo · Node.js · GraphQL · GitLab CI/CD

### A note on this profile

Most of my work lives on a private GitLab. What you find here are side projects and experiments, not the day job.

### Elsewhere

- Portfolio: https://aissoufi.github.io
- LinkedIn: https://www.linkedin.com/in/issoufiadam
