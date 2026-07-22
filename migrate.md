# Rewriting a Codebase with AI

How Anthropic ports a million lines from one language to another in under two weeks — and the process you can copy. The one idea that makes it work: **you don't fix the code, you fix the process that produces the code.**

## The problem

A full rewrite used to be a multi-year, multi-million-dollar bet — the kind of project you defer forever. So teams keep patching the same bug, living with the slow build, and telling themselves the rewrite is impossible.

It isn't anymore. One team at Anthropic used Claude Code to rewrite Bun — about a million lines — from Zig to Rust in under two weeks, with 100% of the existing test suite passing in CI before merge. The token bill was ~$165K. The old price was $3–4M over ~4 years. When the worst case is "delete the branch and try again tomorrow," the whole decision changes.

But the win isn't "point a big model at your repo and pray." It's a specific, mechanical process. Here's the version you can run.

## The core rule

**You don't fix the code. You fix the loop that produced the code.**

Individual failures are the loop's job — fixer agents burn those down automatically. *Your* attention belongs on the patterns. When the same mistake shows up across many files, you don't hand-patch each one. You amend the ONE rule that produced it and regenerate the batch. The rulebook grows; the code never gets hand-patched against it.

## The six steps

### 0. Build the judge FIRST (the prerequisite)

Before you write a single line of the new code, you need an exit condition — a way to *know* when you're done. That's the judge.

- Take your existing test suite and **strip out anything that imports source-language internals** — those tests can't run against the port.
- Rewrite the external-facing tests into assertions that run against **both** the original and the new code. Your old code is the answer key.
- Validate the judge before you trust it: it must **pass** on the original code and **fail** on deliberately broken code.
- If a test can't run on both versions, it's useless as a judge — drop it or rewrite it.

Without this, you never know when the migration is actually finished. Build it first, keep it running the whole time.

### 1. Create the rulebook, dependency map, and gap inventory

- **Rulebook** — one document that answers every translation decision *once*: how do types map, how do errors propagate, what's the naming convention, what idioms replace what. Agents read this instead of guessing per-file.
- **Dependency map** — so you know which files to migrate first and which belong in the same batch. Have an agent write and run a deterministic script to produce it.
- **Gap inventory** — every spot where the target language demands something the source let you keep in your head (ownership, lifetimes, nullability). The rulebook comes first; the gap inventory is defined by what the rulebook's defaults *don't* cover.

### 2. Stress-test the rules

The rulebook has never met real code. Before any fan-out, run a **bakeoff**: two translators on the same file in separate contexts — one follows the rules, one never sees them — and a diff inspector turns every difference into a verdict on a rule. Then a **pilot**: run the real pipeline on a handful of the nastiest files. The only output that survives this stage is *rule changes*.

### 3. Translate everything

- The work queue is **mechanical**: a file is "done" when its output exists on disk. The queue rebuilds from the filesystem every run, so the migration is **resumable by construction**.
- Fan out translators in parallel. Anything a translator can't do confidently gets flagged `// TODO(port):` and deferred.
- Two **adversarial reviewers** per file in separate contexts; disagreements escalate to a third agent.
- **Keep the compiler out of this loop** if it's expensive — defer it to the next step. When a reviewer keeps catching the same mistake, fix the *rule* and regenerate, don't patch the file.

### 4. Compile

One scripted **survey build** runs the compiler across everything and emits the error list as a machine queue, sliced by module. Parallel fixer agents work the queue **without compiler access** — a single build daemon owns the build so the expensive operation runs once per round, not once per agent. Repeat until clean. (If your typecheck is cheap — TypeScript, Go — this step dissolves into step 3: run it inside every unit's loop.)

### 5. Run it

Hello-world, then smoke tests — the cheap end-to-end proof before the expensive one. Group crashes by root cause and let adversarial subagents review the fixes.

### 6. Match behavior

Now the judge from step 0 does its job. Shard the files and run the parity suite against both codebases. Fixer agents triage every failure against the *old* code too (regression / inherited / environment), and adversarial reviewers check their fixes. The old code is the executable spec. Done = the judge passes on the new version, documented.

## Best practices

- **Front-load the human hours.** The rulebook and the stress test are the expensive part. Everything after is queues burning down.
- **Make review adversarial, verification mechanical.** Let a compiler, a diff, or a test suite be the referee — not vibes.
- **Don't use the biggest model for everything.** Token spend concentrates in your loops. Smaller models handle the high-volume translation fan-out fine; save your largest model for reviewers and for anything that writes rules other agents follow.
- **Don't chase individual failures.** That's the loop's job. Watch the patterns; a repeated failure indicts a rule.
- **Make the queue resumable.** "Done" means the output file exists on disk.

## Quick-start checklist

- [ ] Build the judge FIRST — external-facing tests that run on both old and new code; validate it passes on old, fails on broken
- [ ] Write the rulebook (every translation decision, decided once) before touching a file
- [ ] Generate a dependency map to order and batch the work
- [ ] Stress-test the rules with a bakeoff + a pilot run — the only output is rule changes
- [ ] Translate with a mechanical, resumable queue (done = file exists on disk)
- [ ] Two adversarial reviewers per file; a repeated mistake fixes the RULE, not the file
- [ ] Batch the compiler; a single build daemon owns the build
- [ ] Burn down the parity suite against the judge; old code is the spec

## The starter kit

Anthropic published the actual prompts, templates, and scripts behind this process:

**→ [anthropics/code-migration-kit-with-claude-code](https://github.com/anthropics/code-migration-kit-with-claude-code)**

Start with `prompts/00-feasibility.md` — it produces a read-only report on whether you should migrate at all ("don't migrate" is a valid answer). The kit defaults to structure-preserving migrations (same architecture, new language); if you're redesigning as you go, read the "If you're redesigning" section first.

## Key insight

Reliability at this scale isn't a property of the model — it's a property of the *process*. The rulebook is the real artifact; the code is downstream of it. Fix the loop, and every file it produces gets better at once.
