# Hermes Agent Setup Guide

You commented "HERMES" — here's the full framework for managing an AI agent like a new hire.

The core idea: nobody teaches you how to MANAGE an AI agent. The Five Pillars (from Nate Herk) give it structure.

---

## The Five Pillars

### 1. Memory

What it is: persistent context the agent retains between sessions — past decisions, user preferences, project state.

Setup tip: create a `memory/` folder the agent reads on startup; append learnings after each task.

### 2. Skills

What it is: discrete capabilities you teach the agent — specific workflows it can execute on command.

Setup tip: write each skill as a standalone markdown file with trigger phrase, steps, and expected output.

### 3. Soul

What it is: `soul.md` — the agent's identity doc defining personality, boundaries, tone, and decision-making principles.

Setup tip: write who the agent IS (role, values, constraints) not just what it does — keep it under 500 words.

### 4. Crons

What it is: scheduled jobs that run without you prompting — the agent works while you sleep.

Setup tip: start with one daily cron (e.g., morning summary or inbox triage) and expand from there.

### 5. Self-Improving Loop

What it is: the connection between all four pillars — the agent uses memory to refine skills, updates its soul based on corrections, and adjusts cron schedules based on outcomes.

Setup tip: add a "reflect" step at the end of each task that writes what worked/failed back to memory.

---

## "Treat It Like a New Hire" Checklist

- [ ] **Own accounts** — give it dedicated API keys with least privilege (never your personal creds)
- [ ] **Correct it on the spot** — when it does something wrong, tell it immediately so it logs the correction to memory
- [ ] **Write its onboarding doc** — that's `soul.md` — defines role, boundaries, and how it should ask for help
- [ ] **Schedule its shifts** — set up crons so it works autonomously on recurring tasks

---

## Install

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

## Docs

Full documentation: https://hermes-agent.nousresearch.com/docs/
