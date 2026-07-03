# Meta-Prompting & Skillify

How to build a self-generating skill system that turns any repeated task into permanent infrastructure.

## The problem

You're still typing prompts like it's 2024. Every time you need the AI to do something, you write it from scratch. Same question, same context, same format — every single time.

## The framework

Instead of prompting, you build **skills** — tested files that know the triggers, the edge cases, and the exact format you want. Then you build a **meta-skill** that watches what you do and automatically turns successful patterns into new skills.

## Architecture

```
┌─────────────────────────────────────┐
│          THIN ROUTER                 │
│   (dispatches to the right skill)   │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│          FAT SKILLS (100+)           │
│  Each: trigger, edge cases, format   │
│  Tested. Versioned. Permanent.       │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│       FAT DATA (your context)        │
│   100K+ pages of YOUR information    │
└─────────────────────────────────────┘
```

## How to implement it

### Step 1: Turn your first repeated task into a skill

Pick something you ask the AI at least once a week. Write it as a markdown file:

```markdown
# Skill: Weekly Research Summary

## Trigger
When asked to summarize research, news, or updates from the past week.

## Process
1. Search for developments in [your domain]
2. Filter for actionable items only
3. Format as: headline → one-line summary → action item

## Output format
- Max 10 items
- Each item: **Headline** — Summary. → Action: [what to do]
- Sorted by relevance to current projects

## Edge cases
- If fewer than 3 items found, expand the search window to 2 weeks
- If more than 15 items, apply stricter relevance filter
```

### Step 2: Register it in a router

Your agent needs a way to find the right skill. Simple approach:

```markdown
# Skill Router

## Available skills
- weekly-research: Summarize weekly developments
- code-review: Review a PR against team standards
- meeting-prep: Prepare context for a scheduled meeting
- ...

## Resolution
Match user intent to skill name. Load the skill file. Execute.
```

### Step 3: Build the meta-skill (Skillify)

Once you have 5-10 skills working, add one that creates new ones:

```markdown
# Skill: Skillify

## Trigger
When the user says "skillify this" after completing a task successfully.

## Process
1. Review what was just done (the task, the approach, the output)
2. Extract the repeatable pattern
3. Write a new skill file following the standard format
4. Register it in the router
5. Confirm with the user

## Rules
- Only skillify tasks that worked well (don't codify failures)
- Include edge cases discovered during the original task
- Test the new skill immediately with a dry run
```

### Step 4: Feed it YOUR context

The skills work on YOUR data — your projects, your preferences, your history. The more context the system has, the more specific (and useful) each skill becomes.

## Results you can expect

- 100+ skills running a complete workflow
- One skill reads an entire book and maps every idea to your actual life — 22 chapters, a sub-agent per chapter, 30,000 words in 40 minutes
- Every task you repeat becomes permanent infrastructure
- The system gets better every week because Skillify is always watching

## Quick-start checklist

- [ ] Identify 3 tasks you repeat weekly
- [ ] Write each as a skill file (trigger + process + format + edge cases)
- [ ] Create a simple router (just a list mapping intent → skill file)
- [ ] Add the Skillify meta-skill
- [ ] Use it for a week. Say "skillify this" whenever something works
- [ ] After 2 weeks you'll have 10-15 skills running automatically

## Key insight

The LLM is just an engine — you build your own car. Fat skills. Fat data. Thin router. Stop prompting. Start building infrastructure that compounds.
