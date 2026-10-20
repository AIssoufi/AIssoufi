# Continuous improvement

Each session starts from scratch: a mistake made today is made again tomorrow unless something persists. The fleet turns its mistakes and its friction into written changes to its own instructions.

## Lessons

When something goes wrong because of an agent (a correction from the owner, a claim it retracts, a failure its change caused, a tool used wrongly, a convention missed), it fixes the problem first, then:

1. finds the root cause and its class: a knowledge gap, an unchecked assumption, a missed convention, an environment quirk or a slip;
2. records it in a journal of lessons, where a repeat becomes visible;
3. applies a prevention when the mistake is likely to recur, costly, and preventable by a short instruction or an automatic check.

Workers and masters do not write the journal or the instructions themselves: they send the lesson to their parent, up to the super master.

## Where a prevention goes

A triage skill decides before anything is written, so that instructions do not overlap or contradict each other:

| Need | Home |
| --- | --- |
| A convention with its own trigger | a skill, loaded only when the task matches its description |
| A reminder whenever some files are touched | a rule scoped to those paths, which names its skill and repeats nothing |
| A fact of one project | one line of its `CLAUDE.md` |
| Work that needs its own context, model or tools | an agent |
| Something that must happen every time, without judgment | a hook |

One concern per skill, extended rather than duplicated. The owner approved, once and for all, the preventions that change skills, rules and `CLAUDE.md`; a hook or a settings change still needs their yes. For example, a hook now refuses a shell command that gates its next step on the exit code of `tail` or `grep` rather than of the check piped into it: it came from such a lesson.

## A single writer

The super master alone edits the shared skills, rules and agents. Five masters and their workers editing the same files at once would conflict, and one writer keeps them consistent and short. Improvements go into skills and rules that load when needed, never into an always-loaded memory that every turn would pay for.

## The workflow observer

`workflow-observer` reads the transcripts of a master and its workers over a period, with the fleet journal, the pull requests and their CI runs. It queries the transcripts with `jq` rather than reading them, and looks for repeated failures, rework after a `DONE`, CI failures a local check would have caught, questions a skill could answer, wasted tokens and idle workers. It proposes at most five changes, each with the file to change, the exact text, the evidence and the cost. A master runs it after every five merges or once a day; the super master runs it on the whole fleet daily, and applies the sound proposals.

## Being set up: metrics

A metrics script and a `fleet-analyst` agent are being set up to measure each master's efficiency from the journal, the transcripts and GitHub, so that the observer and the owner work from numbers rather than impressions.
