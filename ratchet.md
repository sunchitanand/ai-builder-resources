# The Complexity Ratchet

A framework for using tests as one-way quality gates in AI agent projects.

## The problem

Most AI-assisted codebases start falling apart at moderate complexity. Every feature shipped without a test is a floor with no foundation — one refactor and the whole thing collapses.

## The framework

The **complexity ratchet** is simple: every time an agent finishes a task, it writes a test that locks that behavior in permanently. That test becomes a one-way gate. Quality can go up. It can never go back down.

## How to implement it

### Step 1: Set the rule

Add this to your agent's instructions (CLAUDE.md, system prompt, or equivalent):

```
Every task you complete must include at least one test that verifies the new behavior.
No PR merges without test coverage for the changed code.
```

### Step 2: Make coverage a pre-commit gate

```bash
# .git/hooks/pre-commit or CI check
coverage_threshold=90
current=$(your-coverage-tool --percentage)
if [ "$current" -lt "$coverage_threshold" ]; then
  echo "Coverage dropped below ${coverage_threshold}%. Blocked."
  exit 1
fi
```

### Step 3: Never lower the threshold

This is the ratchet mechanism. Once coverage hits 85%, you set the gate to 85%. When it reaches 90%, you raise the gate to 90%. The gate only moves up.

### Step 4: Let the agent do the work

AI agents don't experience effort. They don't get bored. They don't cut corners at 5 PM. What used to take months of tedious work is now trivial — 90% coverage is no longer a heroic achievement, it's a Tuesday.

## The proof

One team applied this framework to nearly a million lines of code:
- 970,000 lines of code
- 665 test files
- 14 pull requests merged in 72 hours
- All open source

## Why it works

Below 90% coverage, bugs hide in the untested gaps. Above 90%, regression risk drops off a cliff. The nonlinear quality "knee" means the last 10% of coverage provides disproportionate stability.

Tests function as institutional memory that survives context switches — whether that's employee turnover or a new AI agent session picking up where the last one left off.

## Quick-start checklist

- [ ] Add "every task must include a test" to your agent's system prompt
- [ ] Set up a coverage gate in CI (start at your current coverage — don't set 90% day one)
- [ ] After each PR, bump the threshold to match the new coverage floor
- [ ] Never lower it — that's the ratchet
- [ ] Let the agent write the tests — that's the whole point

## Key insight

The ratchet isn't about testing discipline. It's about making quality accumulation automatic. The AI does the work, the gate ensures it never undoes previous work. Compound quality, not compound bugs.
