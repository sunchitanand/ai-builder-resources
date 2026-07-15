# The Taste Index

A framework for giving an AI agent judgment instead of just more memory.

## The problem

Everyone is trying to make agents remember more — bigger context windows, auto-memory, save-everything logging. But an agent that remembers everything doesn't understand anything. Memory tells an agent *what happened*. It doesn't tell it *what matters*.

If everything goes in, nothing has signal. The agent may remember more, but it will understand less.

## The idea

A **Taste Index** is a structured record of your judgment — a *smaller, sharper* memory, not a bigger one. Instead of logging every link, note, and conversation, you capture only the things that carry a signal about what you actually value.

> Memory tells an agent what happened. Taste tells it what matters.

## The one rule

**No signal, no storage.**

A random link pasted into a chat does not get saved by default. Nothing gets stored unless it passes an explicit taste event.

## Step 1: Pick your trigger phrases

Capture is triggered by explicit signals only — "taste events." Choose a phrase (or a few) that mean *"this one matters"*:

```
"save this"
"I like this"
"this is useful"
```

When you say the trigger phrase, the agent captures. When you don't, it doesn't. That's the whole gate on the input side.

## Step 2: Fill only the two fields that matter

When a taste event fires, capture just two things:

| Field | What goes in it |
|-------|-----------------|
| **What I liked** | The specific thing — the pattern, the phrasing, the decision, the approach. |
| **Why it's useful** | The judgment. Why this is worth keeping when most things aren't. |

> The link is the receipt. The why is the asset.

The link alone is worthless — it's just proof something happened. The *why* is the part that teaches a future agent your taste.

## Step 3: Apply the gate question

Before anything is stored, ask one question:

> **Will this help a future agent make a better choice?**

If yes → store it. If no → let it go, even if it felt useful today.

**What passes:** a decision you'd want repeated, a pattern you want reused, a phrasing you liked, a principle you'd defend.

**What fails:** meeting notes, debugging logs, an article you skimmed once. Useful today, but they say nothing about your judgment — so they don't belong in the index.

## Why the smaller memory wins

A bloated memory drowns the signal. A Taste Index stays sharp because every entry earned its place by passing the gate. The agent doesn't get more data — it gets *your judgment*, distilled. That's what lets it choose well when you're not there to steer it.

## The template (copy this)

```
## Taste event — <date>

**What I liked:** <the specific thing>

**Why it's useful:** <the judgment — why this beats the 100 things I didn't save>

**Gate check:** will this help a future agent make a better choice? <yes / no>
(if no — don't save it)
```

Start tonight: pick one trigger phrase, capture the next three things that genuinely pass the gate, and delete anything that doesn't. A week in, your agent won't remember more — it'll understand you better.
