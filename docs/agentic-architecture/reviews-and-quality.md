# Reviews and quality

## Two independent reviewers

Before each pull request, the worker launches two agents in parallel. They start without the worker's conversation, only with a written brief, so the reasoning that produced the change does not bias their judgment. Running in parallel, the review costs the time of the slower one.

| Reviewer | Checks |
| --- | --- |
| `conventions-reviewer` | The diff against every rule, the skills the rules point to, and the project's `CLAUDE.md`. It quotes the sentence each finding breaks: without a quote, a finding is only a suggestion. It also runs the JSDoc checker, and the lint, type check and tests unless the author ran them green on the same commit. |
| `visual-tester` | The requested behaviors in a browser, the look of every route at 390, 768, 1280 and 1920 px taken in one Playwright command, and the regressions: sideways scrolling, console errors, failed requests. A criterion it could not reach is "not tested", never "passed". |

The visual tester is skipped, with the reason given, when the diff cannot change what a visitor sees (CI, docs, scripts, tests alone).

The worker verifies each finding at its line or on its screenshot, fixes the confirmed ones, then re-runs only the reviewer concerned, once, on the fix commits. A full second review would cost as much as the first and find little more.

## The master's check

The master merges nothing on a worker's word. It reads the pull request on GitHub: the checks, the mergeable state, the `Closes` reference, the diff against the ticket, and the verdict in the body. A claim it relays to the owner, such as "CI is green", is one it checked itself.

## The auditor

`repo-auditor` finds what is wrong with a repository, so that its master can make tickets of it. It runs in a throwaway clone, never in the checkouts other agents use. It installs, lints, tests and builds; reads the CI runs; checks dependencies, Node.js versions, the live deployment, committed secrets, accessibility and performance. Every finding carries the command and the output lines that prove it, or it is dropped. It returns at most 15 findings, ranked P0 to P3, and marks secrets and vulnerabilities as confidential, so they never reach a public issue.

## Guardrails in the code

A convention that matters is also enforced by the repository, so an agent that misses it fails the CI rather than the review: lint rules, type checks, tests of the design tokens, browser tests of the layout and accessibility. Each project's page lists its own, for example [issoufi.fr](https://github.com/AIssoufi/issoufi.fr/blob/develop/docs/ai-workflow/README.md#guardrails-in-the-repository).
