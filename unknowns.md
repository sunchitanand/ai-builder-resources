# Finding Your Unknowns — The Field Guide

Based on [Thariq Shihipar's "Field Guide to Fable"](https://x.com/trq212/status/2073100352921215386) — Claude Code engineer at Anthropic.

---

## The Core Idea

> "The bottleneck of work quality depends on my ability to clarify its unknowns."

Your prompts are a **map**. The actual work is the **territory**. The gap between them = your **unknowns**. Every time AI hits an unknown, it guesses your intent instead of asking.

More complex work = more unknowns = more deviation from what you wanted.

---

## The Four Quadrants

| | You know it | You don't know it |
|---|---|---|
| **Explicit** | Known Knowns (in the prompt) | Known Unknowns (gaps you're aware of) |
| **Implicit** | Unknown Knowns (obvious to you, invisible to AI) | Unknown Unknowns (blind spots — the critical ones) |

---

## The Specificity Paradox

- Too specific → AI follows rigidly even when pivoting would be better
- Too vague → AI makes decisions based on generic defaults that don't fit YOUR task
- "When you don't account for your unknowns you fail both ways"

---

## Five Pre-Implementation Techniques

### 1. Blindspot Pass (the killer move)
Ask AI to identify your unknown unknowns before starting:

```
I know nothing about the auth module. Do a blindspot pass — 
find my unknown unknowns so I can prompt you better.
```

### 2. Brainstorming & Prototyping
Generate multiple radically different directions as HTML artifacts. Don't commit to one approach until you've seen three.

### 3. Interviews
Let AI ask YOU questions one at a time, prioritizing questions whose answers would change the architecture.

### 4. References
Hand it source code as reference — even in a different language. Code is the best reference (not vague descriptions).

### 5. Implementation Plans
Focus on parts most likely to change: data models, types, UX. Push mechanical refactoring to the end.

---

## During Implementation

Maintain `implementation-notes.md` — log every deviation and WHY:

> "If you encounter an edge case forcing you to deviate, choose the conservative option, record it, continue."

---

## After Implementation

- **Pitch docs** — bundle prototype + spec + notes for reviewers
- **Quizzes** — AI generates a quiz on the changes; don't merge until you pass it

---

## The One Prompt That Saves Hours

```
I know nothing about this module. Find my unknown unknowns 
so I can prompt you better.
```

That single line turns a 4-hour debugging session into a 20-minute conversation.

---

## Key Takeaway

> "Every explainer, brainstorm, interview, prototype, and reference is a cheap way to find out what you didn't know before it gets expensive to fix."

The more powerful the models get, the more you can achieve with the right approach. The best AI coders don't write better prompts — they eliminate their blind spots first.

---

*Source: [Thariq Shihipar's Field Guide](https://x.com/trq212/status/2073100352921215386) | [Companion examples](https://thariqs.github.io/html-effectiveness/unknowns/)*
