# Traceability

With a dozen sessions working at once, a merged pull request or a closed issue must lead back to the agent that did it, and to the transcript of its reasoning. `fleet.mjs`, a small Node.js script of the fleet skill, keeps two append-only files for that.

| File | Answers | One line per |
| --- | --- | --- |
| `~/.claude/fleet/agents.jsonl` | who was who | session: name, role, repository, parent agent, effort, workspace, session ID and transcript path |
| `~/.claude/fleet/actions.jsonl` | who did what | action: the same identity, the action, its target and a note on why |

## Commands

```bash
fleet.mjs register                                 # record this session in the registry
fleet.mjs log pr.merge AIssoufi/chess#12 --note "CI green, review clean"
fleet.mjs show --since 2026-10-20 --agent MASTER-CHESS
fleet.mjs agents --repo AIssoufi/chess             # one line per session
fleet.mjs start-master MASTER-CHESS ~/Sources/chess --effort high
```

Every master and worker registers at its start. Masters log each action they take: worker starts and stops, issues created or closed, findings rejected, pull requests merged or rejected, branches deleted, deployments, questions to the owner and their answers, audits and observer runs. Workers log the pull requests they open. `start-master` logs the start of a master itself.

## Design choices

- **JSON Lines, append only.** One small append per call, so the lines of agents writing at the same time never mix, and no line is ever rewritten.
- **Each line reads alone.** It repeats the agent's identity rather than pointing to the registry, so a filtered extract still says who acted.
- **A small vocabulary.** Actions are written `object.verb` (`pr.merge`, `owner.decision`), so they can be counted and filtered.
- **Identity from the environment.** The name, role, parent and workspace are set when a session is started; the script reads them, with the repository from the Git remote and the transcript found from the session ID.

## Other traces

- **On GitHub**: the branch, the commit footers (`Refs: ISF-35`) and the pull request (`Closes #35`) all carry the ticket reference, which an autolink turns into a link.
- **Per master**: a handover file, read at its start, and a short state file it keeps current with its priorities, its running workers, the pull requests waiting and why, and its next steps. It survives a compaction, and the super master reads it.
- **Lessons**: a journal of mistakes and their preventions, see [Continuous improvement](continuous-improvement.md).
