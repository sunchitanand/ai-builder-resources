# Claude Code's Built-In Second Brain

Everyone's building complex Obsidian vaults and custom cron scripts to create a "second brain" for AI. Claude Code already ships all 4 pieces. You don't build a second brain. You configure one that's already running.

## The 4 pieces

Every "second brain" framework (Nate Herk's 4 Cs, the viral Fable 5 thread, the llm-wiki pattern) boils down to:

1. **Routing file** — tells the agent where everything lives
2. **Memory** — captures what worked, what failed, what to never repeat
3. **Skills** — reusable workflows, not just prompts
4. **Background loops** — agents that run while you sleep

Claude Code ships all four out of the box.

---

## Piece 1: The Routing File (CLAUDE.md)

Every project gets a `CLAUDE.md` in the root. It's not a knowledge dump — it's a MAP.

```markdown
# My Project

## Docs Map
- `docs/architecture.md` — system design decisions
- `docs/api.md` — endpoint reference
- `.claude/skills/` — reusable workflows

## Rules
- Always run tests before committing
- Use conventional commits
- Never modify shared config without a migration
```

**What it does:** When Claude Code starts a session, it reads this file first. Every instruction, every pointer, every constraint routes through here.

**Setup:** Create `CLAUDE.md` in your project root. Start with 3 sections: what this project is, where things live, what rules to follow.

---

## Piece 2: Memory (auto-compounding knowledge)

Location: `~/.claude/projects/<project-id>/memory/`

This folder updates itself between sessions. No cron job. No manual wiki edits.

**Types of memory:**
- `user` — your role, preferences, expertise level
- `feedback` — corrections you've given (so they stick permanently)
- `project` — ongoing work, decisions, deadlines
- `reference` — where to find things in external systems

**How it works:**
1. You correct Claude ("don't mock the database in tests")
2. It saves a memory: "integration tests must hit a real DB — mocks burned us last quarter"
3. Every future session loads that memory automatically
4. The correction never needs repeating

**Setup:** Just use Claude Code normally. Memory builds itself from corrections and context. To save something explicitly: "remember that deploy keys rotate monthly."

---

## Piece 3: Skills (workflow specifications)

Location: `.claude/skills/<skill-name>/SKILL.md`

These aren't prompts you copy-paste. They're full workflow specs with prerequisites, gates, and bookkeeping.

```markdown
# /deploy

## Prerequisites
- All tests passing
- No uncommitted changes
- AWS credentials fresh (< 12h old)

## Steps
1. Run the full test suite
2. Build the production bundle
3. Upload to S3
4. Invalidate CloudFront cache
5. Verify the health endpoint responds 200

## After
- Update the deploy log with timestamp + commit SHA
- Notify #deploys channel
```

**What it does:** Type `/deploy` and the entire workflow runs — checks, gates, and all. Not "here's a prompt that might work." A repeatable specification.

**Setup:** Create `.claude/skills/` in your project. Start with one skill for your most repeated task (deploy, review, test, release). Add steps, prerequisites, and any bookkeeping.

---

## Piece 4: Background Loops (cadence)

The Workflow tool fans out agents that run while you sleep, writing results back to disk.

**What this looks like:**
- A workflow that audits your codebase for stale TODOs every night
- Parallel agents that research 5 topics simultaneously
- A loop that keeps running until it finds 10 bugs, then stops
- A recurring check that verifies your deploy is healthy

**How it works:**
```
You: "Run a workflow that checks all open PRs for merge conflicts and flags any that need rebasing"

Claude Code: spawns parallel agents → each checks one PR → writes results to a file → you wake up to a clean summary
```

**Setup:** Use the Workflow tool for any task that benefits from parallel execution or unattended operation. Start with something simple: "check all my open PRs and summarize their status."

---

## The compound effect

Here's why this beats a custom-built system:

| Custom "second brain" | Claude Code built-in |
|---|---|
| Manual Obsidian vault maintenance | File system IS the vault |
| Nightly compile scripts | Memory auto-updates on session close |
| Separate wiki app | `[[wikilinks]]` in memory files |
| Hand-written routing config | CLAUDE.md generated alongside project |
| Prompts saved in a folder | Skills = full workflow specs with gates |
| Custom cron for background tasks | Workflow tool + background agents |

Each session adds to memory. Each correction sticks permanently. Each skill gets refined. The system compounds without maintenance.

---

## Quick start (5 minutes)

1. **Create your routing file:**
   ```bash
   touch CLAUDE.md
   # Add: project description, where docs live, key rules
   ```

2. **Let memory build naturally:**
   - Use Claude Code for a few sessions
   - Correct it when it does something wrong ("don't do X, do Y instead")
   - Check `~/.claude/projects/` to see what it learned

3. **Write your first skill:**
   ```bash
   mkdir -p .claude/skills/my-first-skill
   # Create SKILL.md with steps for your most repeated task
   ```

4. **Try a background loop:**
   - Ask Claude Code to run something in the background
   - "Check all files in src/ for TODO comments and list them"

That's it. Four pieces. Zero external tools. The second brain is already running — you just need to point it in the right direction.

---

## Resources

- [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code)
- [CLAUDE.md reference](https://docs.anthropic.com/en/docs/claude-code/memory)
- The original viral thread: [@EXM7777](https://x.com/EXM7777/status/2073045719020343705) (794K views, 5.3K bookmarks)
- Nate Herk's "4 Cs" framework: Context → Connections → Capabilities → Cadence
