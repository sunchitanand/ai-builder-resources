# The LLM/Code Boundary

How to make an unreliable AI agent reliable by taking work *away* from the model — the architecture pattern behind Uber's grocery-cart agent.

## The problem

Your agent works in the demo and fails in production. The instinct is to reach for a bigger model. That rarely fixes it, because the failures usually aren't "the model isn't smart enough" — they're "the model was asked to do something that has to be *exact*, and language models are not exact."

Prices drift. Quantities come out wrong ("12 eggs" becomes 12 cartons). Totals don't add up. Out-of-stock items slip through. Every one of those is a task where *close* is a bug, and LLMs are probabilistic — they give you *close*.

## The framework

Split every job your agent does into two buckets:

- **Meaning (give to the LLM):** the fuzzy, ambiguous, judgment work. Reading what the user *meant*. Deciding which product matches "the good olive oil." Interpreting intent. There's no single correct answer, so a probabilistic model is the right tool.
- **Truth (give to code):** anything that must be *correct*. Arithmetic, prices, availability, quantities, totals, validation, lookups. There *is* a single correct answer, so a deterministic function is the right tool — and the model never touches it.

> The rule to steal: **LLM handles meaning. Code handles truth.**

## How Uber's cart agent actually works

The agent that builds a grocery cart from a photo of a handwritten list is *mostly deterministic code*, with the LLM confined to a narrow slot:

1. **Read the list (LLM — meaning).** Vision + language model turns the messy photo into structured items. This is genuinely fuzzy: handwriting, abbreviations, crossed-out lines.
2. **Emit a plan, don't go shopping (LLM — structured output).** The planner LLM does *not* place orders. It outputs a structured `CartPlan`: for each item, a `search_terms`, an `item_context` (what the user probably meant), and `price`/quantity constraints. That's it. It hands the plan to code.
3. **Resolve quantities in code (deterministic).** "12 eggs" is resolved to *one carton of twelve* by a deterministic quantity step — not by asking the LLM to do arithmetic. A language model doing math that has to be right is a latent bug.
4. **Price, match, check stock (deterministic).** Real product lookups, real prices, real availability, the running total — all plain code the model never sees.
5. **Guardrail + fallback (deterministic).** If any step returns something broken or implausible, a deterministic guardrail vetoes it and falls back *before* it ever reaches your cart. The user never sees the bad output.

Most of the "boxes" in that system are code. The LLM is a small, well-fenced component that only does the one thing it's actually good at.

## How to apply it to your own agent

### Step 1: List every distinct thing your prompt does right now

Write out each responsibility your single mega-prompt is currently carrying. Most "one prompt does everything" agents are secretly doing 6–10 jobs.

### Step 2: Label each one — meaning or truth?

For each responsibility, ask: *is there a single correct answer?*
- **No / it's a judgment call** → meaning → keep it on the LLM.
- **Yes / close is a bug** → truth → it must move to code.

### Step 3: Make the LLM emit structured output, not actions

Instead of letting the model "do the task," have it emit a typed plan (JSON with a schema) describing what *should* happen. Code executes the plan. This is the single highest-leverage change: it turns the LLM from an unpredictable actor into a predictable *proposer*.

### Step 4: Move every "truth" job into deterministic functions

Math, lookups, validation, formatting, totals — plain code. The model never computes a number that has to be right.

### Step 5: Add a deterministic guardrail

A validation layer that can reject a bad model output and fall back to a safe default *before* the user sees it. Cheap, deterministic, and catches the long tail.

## Quick-start checklist

- [ ] List every distinct job your current prompt is doing
- [ ] Label each: does it have a single correct answer? (truth) or is it a judgment call? (meaning)
- [ ] Keep ONLY the meaning/ambiguity jobs on the LLM
- [ ] Have the LLM emit a structured plan (typed JSON), not take actions directly
- [ ] Move every "truth" job — math, prices, validation, lookups — into deterministic code
- [ ] Add a guardrail that can veto a bad output and fall back before the user sees it
- [ ] When it's still unreliable, make the model do LESS — don't reach for a bigger model first

## Key insight

Reliability isn't a property of the model — it's a property of the *architecture*. A smaller model with a tight, well-fenced job beats a bigger model asked to be exact. Every responsibility you move out of the LLM and into deterministic code is one fewer place your agent can be *close but wrong*.
