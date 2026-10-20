# Continuous improvement

Each session starts from scratch: a mistake made today is made again tomorrow unless something persists. The fleet turns its mistakes and its friction into written changes to its own instructions.

## Lessons

When something goes wrong because of an agent (a correction from the owner, a claim it retracts, a failure its change caused, a tool used wrongly, a convention missed), it fixes the problem first, then:

1. finds the root cause and its class: a knowledge gap, an unchecked assumption, a missed convention, an environment quirk or a slip;
2. records it in a journal of lessons, where a repeat becomes visible;
3. applies a prevention when the mistake is likely to recur, costly, and preventable by a short instruction or an automatic check.

Workers and masters do not write the journal or the instructions themselves: they send the lesson to their parent, up to the super master, which hands it to the gardener (see below).

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

## A single writer: the skill gardener

The shared skills, rules and agents are edited by the super master, through one `skill-gardener` at a time. Five masters and their workers editing the same files at once would conflict, and one writer keeps them consistent and short. Improvements go into skills and rules that load when needed, never into an always-loaded memory that every turn would pay for.

1. **Queue.** The super master writes each lesson and each proposal, with its sender and its evidence, as one line of a queue file. The file survives a compaction of its context, which is the scarcest of the fleet: that is why it does not write the changes itself.
2. **Garden.** When lines are waiting and no gardener runs, it launches one in the background, and edits nothing while it runs. The gardener checks each item's evidence, looks for a repeat in the journal, chooses the mechanism through the triage skill, applies it (a script change with its tests), journals the lessons and logs each change in the fleet journal. It never applies a hook or a settings change: it returns them as proposals for the owner.
3. **Report.** Its reply gives one line per item, applied or rejected with the reason, and the agents to tell, since a running master keeps the charter it started with.

Once a day, the gardener also makes a hygiene pass: it measures the instructions (line counts, files over their budget, sentences repeated across files, overlapping descriptions), fixes the three worst and lists the rest. A rule stays under about 20 lines and names its skill, a skill's body under about 200 lines with its details in reference files read on demand, so the instructions stay short and split.

## The workflow observer

`workflow-observer` reads the transcripts of a master and its workers over a period, with the fleet journal, the pull requests and their CI runs. It queries the transcripts with `jq` rather than reading them, and looks for repeated failures, rework after a `DONE`, CI failures a local check would have caught, questions a skill could answer, wasted tokens and idle workers. It proposes at most five changes, each with the file to change, the exact text, the evidence and the cost. A master runs it after every five merges or once a day; the super master runs it on the whole fleet daily, and queues the sound proposals for the gardener.

## Being set up: metrics

A metrics script, `metrics.mjs`, and a `fleet-analyst` agent measure each master's efficiency from the journal, the transcripts and GitHub, so that the observer and the owner work from numbers rather than impressions.
