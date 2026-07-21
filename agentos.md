# The Agent OS — Give Every Decision One Owner

Your agent doesn't need to be smarter. It needs a boss.

## The problem

You wired your agents into a graph — a planner, a builder, a reviewer, a tester. That was the right move. But a graph only shows *who is connected to who*. It never says who is in **charge**.

So one night you leave a coding agent running: write code → send to reviewer → run checks → fix on failure → repeat. You wake up to **30,000 lines of new code** for a small problem. Nothing crashed. Every check passed. The app is broken anyway.

The loop *succeeded*. The system *failed* — because nothing in it was allowed to stop and ask the one question that mattered: **"are we still solving the right problem?"** The agent just kept patching its own patches. Every patch was locally reasonable. All of it together was garbage.

Connecting agents is not the same as governing them. A team with nobody in charge doesn't stop. It just fails faster, in parallel.

## The fix: every decision has exactly one owner

This is the whole framework. For every decision your agent system makes, it belongs to **CODE**, the **MODEL**, or **YOU** — never a mix, never "whatever the LLM feels like."

### 1. CODE owns anything countable

If the answer is a fact, it's an `if` statement — not a job for a model.

| Decision | Owner | Why |
|----------|-------|-----|
| Budget blown? | **Code** | It's a number vs a limit |
| Retried more than N times? | **Code** | It's a counter |
| Valid JSON / compiles / tests pass? | **Code** | It's parseable, deterministic |
| File exists / permission granted? | **Code** | It's a lookup |

Never ask a model to check what code already knows. It's slower, it costs tokens, and it **guesses** — a model can call broken JSON "mostly fine." A single hard limit (retry ceiling, token cap, budget gate, max-lines-changed) would have stopped that 30,000-line spiral for free.

> **Use code for certainty. Use the model only for ambiguity.**

### 2. The MODEL owns anything ambiguous

The genuinely fuzzy calls — the ones with no parseable answer:

- *Is this code actually good, or just passing the check?*
- *Why did this keep failing — the plan, the reviewer, or the goal?*
- *Does this output actually make sense for the user?*

Let the model **diagnose**. Never let it **decide alone** on anything high-stakes. A model can be perfectly, confidently consistent with its own misunderstanding — and stacking a second model to "supervise" the first just raises the question of who supervises the supervisor. The model proposes; code enforces limits; you hold final say.

### 3. YOU own the mission

Not an approve button. Not a rubber stamp after the agent's already off and running. You own **what "done" actually means.**

- The agent may rewrite its **plan** all day — that's its job.
- The agent may **never** rewrite the **mission** — that's yours.

The moment an agent can move the goalposts to make itself "win," it has stopped solving your problem and started redefining success. Lock the goal. Start the run with a short **clarification** step — turn a vague ask ("build me something that makes money") into a written spec you both agree on — *before* any planning. We test our code obsessively and let the agent's understanding of the goal go completely untested. Test the understanding first.

## Apply it tonight (checklist)

- [ ] **List the last 10 decisions** your agent made on its own. Tag each: countable, ambiguous, or mission.
- [ ] **Move every countable one into code** — a hard `if`, a limit, a gate. Delete the "ask the model to verify" step.
- [ ] **Add one hard STOP.** A retry ceiling OR a budget cap OR a max-lines-changed limit. Just one, today. This alone kills runaway spirals.
- [ ] **Give the model exactly one ambiguous job** (usually: "diagnose why this failed") — and strip its authority to *decide* the next high-stakes move alone.
- [ ] **Write the mission down** as one sentence of "done," and make it read-only to the agent. Add a clarification step before planning.

## Why this works

You've already built half of an operating system by accident. Checkpoints, retries, permission prompts, sandboxes, saved state — those aren't "framework features," they're **OS features**. You added them one patch at a time without noticing the pattern.

Frameworks (LangGraph, AutoGen, etc.) give you **mechanisms** — they *let* you set a retry, write to memory, add a human interrupt. An operating system is where those become **enforceable policy** — *how many* failures escalate, *who* may write memory, *which* actions need your approval, *who* can change the goal. Same reason a real OS decides who may spawn a process, not just that processes exist.

A graph **connects** your agents. An operating system **runs** them.

## Key insight

> Graphs are not the destination. They're the language of the next operating system. You stopped building isolated agents a while ago — you're building operating systems for intelligence now, whether you named it or not.

Give every decision an owner. That's the first line of the OS.
