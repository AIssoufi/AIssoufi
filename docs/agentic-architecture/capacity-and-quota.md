# Capacity and quota

The fleet shares one subscription and one machine. Each worker builds, runs a dev server and opens a browser for its tests, and each spends quota on every turn. Two limits follow: how many workers run at once, and how fast the quota goes.

## Today

- **A cap of six workers** on the machine, across all masters, with a per-master share in each handover. Before each start, a master counts the running workers from the process list.
- **Effort per ticket**, not one effort for all: see [Roles](roles.md). A missed convention costs a CI run, a review and a rebase, while the highest effort on every ticket spends the quota.
- **A checkpoint every 30 minutes**, in each master and in the super master: answer the messages, merge the ready pull requests, clean up, start the next tickets by priority, and restart an agent stopped on a usage limit once it resets.
- **Token habits** written into the charters: long outputs go to a file and only their tail is read, a diff is read by its stats first, and a master's context window is smaller than a worker's.

## Being set up: the quota steward

The steward's script, `usage.mjs`, exists; the agent that runs it at each checkpoint of the super master is being set up. The script:

1. reads the plan usage from Claude Code's `/usage` panel, typed in an idle session kept open for that purpose, so a reading spends no token and no credential is ever read;
2. reads the free memory and the load of the machine;
3. writes `~/.claude/fleet/capacity.json`, which caps every master, with a mode, a number of workers and the lowest priority to start.

| Mode | Workers | Priorities started | When |
| --- | --- | --- | --- |
| `save` | 2 | P0 and P1 | a window is at 90% or more, or ahead of its pace (the week by 10 points, the five-hour session by 20) |
| `normal` | 6 | all | otherwise, and whenever the usage cannot be read |
| `push` | 8 | all | the week is behind its pace (10 points after half the week, 25 at any time), and the machine has room |

The pace compares the used share of a window with the share of its time gone. Saving early matters most for the week, since its limit stops the fleet for days. Pushing spends quota that the reset would otherwise lose.

The machine caps every mode: low free memory or a high load lowers the number of workers to four, then two, since browser tests start to time out when the machine swaps.
