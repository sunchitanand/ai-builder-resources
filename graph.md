# From Loops to Graphs — Stop Wiring One Agent at a Time

A loop is one worker. A graph is the whole team, organized.

## The problem

You got good at loops. One agent, one context, one eval: trigger → act → verify → retry → exit. That was the single-agent era, and it's a solved problem.

The real question now isn't "can one agent keep reasoning?" It's **how do ten agents share a goal without stepping on each other?** The moment you have a planner, a builder, a reviewer, and a tester, "think, act, observe, retry" stops describing the system. What matters is how the roles *relate* — who depends on who, what runs in parallel, who can veto, which failures spread downstream.

That's graph engineering. And the part nobody tells you: **you're not running one graph. You're running two at the same time.**

## The two graphs

### 1. The ORG graph — who always exists

Your permanent roster. Long-lived agents with named roles: a researcher, a validator, a fixer, a planner. Each box runs its own loop. These roles don't change between tasks.

Think of it as your team's org chart — except every box is an agent. Stable, structural, geometric. It answers one question: **WHO exists.**

- Named roles with clear ownership (this agent owns tests, that one owns research)
- Preserved memory per role — the validator remembers how it validates
- Stable edges — the handoff paths between roles don't move mid-run

### 2. The WORK graph — what's happening right now

This one rewrites itself mid-run. Your researcher finds a bug → routes it to the fixer → the fixer's patch goes to the tester → the tester finds a regression → the graph spawns a **new node you never planned.**

Tasks appear, split, merge, and die based on what the agents actually discover. It's your sprint board — except the board rewrites itself. It answers: **WHAT is happening, right now.**

- Ephemeral task nodes that exist only as long as the work
- Edges that split/merge/reorder as evidence arrives
- No fixed shape — the topology *is* the current state of the work

> The org graph answers **WHO**. The work graph answers **WHAT, right now.** You need both, and they are not the same diagram.

## Why graphs are harder than loops

A loop lets you defer architecture. A graph forces you to declare the structure upfront. Three things get harder the moment you go multi-agent:

| Loop | Graph |
|------|-------|
| One agent fails → one agent broke | One node fails → **every downstream agent works off stale inputs** — and none of them know it |
| Context is one continuous thread | Context does **not** flow between nodes automatically — you have to design the edges |
| No coordination cost | You must map agents → domains and define every handoff protocol upfront |

The failure surface is the killer. In a loop, a break is local and loud. In a graph, one node fails silently and three agents downstream keep working confidently on bad inputs. That's not a bug you see — it's a bug that compounds.

## Apply it tonight (checklist)

- [ ] **Draw your ORG graph.** List every long-lived role your system has (or should have): researcher, planner, builder, reviewer, tester. One box each. This is your permanent roster.
- [ ] **Draw your WORK graph for one real task.** Trace how a single piece of work actually flows: which node starts it, where it routes on success, where it routes on failure, where a new node might spawn.
- [ ] **Design the edges, not just the nodes.** For every handoff, write down *what data crosses it.* Context doesn't travel for free — if the tester needs the plan, that's an edge you build.
- [ ] **Plan for one node failing.** Pick any node and ask: if this returns garbage, who downstream is now poisoned? Add a check on that edge before the bad output spreads.
- [ ] **Name who can stop the whole thing.** In a loop it's the exit condition. In a graph, decide explicitly which node (or you) can halt the run.

## Why this works

Loops made agents *adaptive* — they could act, observe, and retry instead of following a fixed script. Graphs make agents *organized* — they turn a pile of capable workers into a team with structure.

The mistake is thinking more agents = better. More agents just means more roles. Without shared state, defined edges, and a failure plan, adding agents increases coordination cost and widens the surface for silent failure. A single agent with a long context often beats a badly-wired graph. The graph only wins when you actually design it.

## Key insight

> A loop is one worker doing its job. A graph is the whole team, organized. If you're still wiring one loop at a time, you're writing subroutines while the field moved to programs.

Stop optimizing the single worker. Start designing the team.

---

*Next: once you have a graph, the question becomes who's in charge of it — that's the [Agent OS](agentos.md) (keyword AGENTOS).*
