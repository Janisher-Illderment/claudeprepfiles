# START HERE
## Your First 10 Minutes with ClaudePrepFiles

---

## What You Have

You've found a **professional configuration system for Claude Code** — 17 files that transform how Claude Code behaves when you work with it.

This is not a list of tips. It's an installable system that changes Claude's default behavior at a fundamental level.

---

## What Problem This Solves

**Before ClaudePrepFiles:**
- Claude invents APIs and function signatures with false confidence
- Claude runs destructive commands without warning
- Claude gives padded, verbose responses when you need direct answers
- Each session starts from zero with no project context
- No guidance on when to use Opus vs Sonnet vs Haiku (the expensive default)
- No structure for team-wide consistency

**After ClaudePrepFiles:**
- Claude marks `[UNCERTAIN]` instead of fabricating
- Claude confirms before anything irreversible
- Claude leads with the answer, skips the preamble
- Project CLAUDE.md gives Claude permanent context about your codebase
- Model selection strategy saves budget and improves output quality
- Your whole team uses Claude the same way

---

## The 3 Most Important Files

If you only have 30 minutes, focus on these:

1. **`global_CLAUDE_ENHANCED.md`** — The config to install. Copy to `~/.claude/CLAUDE.md`. Everything else explains and extends this file.

2. **`QUICK_START.md`** — How to install it in 5 minutes. Read this first.

3. **`project_CLAUDE_ENHANCED_TEMPLATE.md`** — Copy to any project's `.claude/CLAUDE.md` and fill in your project's context. 30 minutes of setup saves hours of Claude making wrong assumptions.

---

## Your First Action (Do This Now)

```bash
# 1. Install the global config:
mkdir -p ~/.claude
cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md

# 2. Verify in Claude Code:
# Type: "What are your operating principles for this session?"
# You should see references to [UNCERTAIN] markers, model selection, and
# confirming before destructive operations.
```

That's it. Claude Code's behavior changes immediately.

---

## 5 Things That Change Immediately After Installation

1. **Uncertain answers get labeled** — `[UNCERTAIN]` appears before guesses instead of confident-sounding fabrications.

2. **Destructive operations pause** — Claude describes what it will do and waits for confirmation before `rm`, force-push, database drops.

3. **Responses get shorter** — No more "Certainly! I'd be happy to help! Let me explain what I'll do..." — just the answer.

4. **Assumptions surface** — Instead of silently guessing your framework version or import path, Claude states its assumptions.

5. **Model awareness kicks in** — Claude will reason about what model depth the task actually needs (especially useful when Opus is your default).

---

## How the System Connects

```
You install this once (global)
        │
        ▼
~/.claude/CLAUDE.md ◄── global_CLAUDE_ENHANCED.md
        │
        │ applies to all projects
        │
        ▼
Per-project: .claude/CLAUDE.md ◄── project_CLAUDE_ENHANCED_TEMPLATE.md
        │
        │ overrides/extends global for this project
        │
        ▼
Better Claude Code output for this specific codebase
```

The **global config** sets professional standards. The **project config** adds codebase-specific knowledge (architecture, gotchas, conventions) that no generic config can know.

The **AI Agent files** are optional advanced reading — they apply the same professional standards to Claude's own internal reasoning.

---

## Need Help?

| Question | Where to Look |
|----------|--------------|
| How do I install? | QUICK_START.md, INSTALLATION.md |
| What changed in March 2026? | MARCH_2026_UPDATE_SUMMARY.md |
| Upgrading from older config | TRANSITION_GUIDE.md |
| Setting up for my team | INSTALLATION.md → Team Setup |
| How to contribute | CONTRIBUTING.md |
| I'm confused about the AI Agent files | claude_code_AGENT_PROTOCOL.md (start there) |
| Something seems wrong | Open an issue on GitLab |

---

## Complete File Map

| File | One-Line Description |
|------|---------------------|
| README.md | Project overview, features, FAQ |
| QUICK_START.md | 5-minute install guide |
| INSTALLATION.md | Setup for individuals, teams, CI/CD |
| **global_CLAUDE_ENHANCED.md** | **The config — install this** |
| project_CLAUDE_ENHANCED_TEMPLATE.md | Per-project config template |
| MARCH_2026_UPDATE_SUMMARY.md | What changed in March 2026 |
| TRANSITION_GUIDE.md | Upgrading from v1.0 |
| CONTRIBUTING.md | How to contribute |
| UPLOAD_GUIDE.md | Deploy to your team |
| INDEX.md | Complete file reference |
| 00_START_HERE.md | This file |
| UPLOAD_INSTRUCTIONS.txt | Copy-paste git commands |
| LICENSE | MIT License |
| .gitignore | Standard ignore patterns |
| claude_code_AGENT_PROTOCOL.md | How Claude governs itself |
| claude_code_AGENT_PATTERNS.md | 14 reasoning patterns for AI operation |
| claude_code_AGENT_SELF_IMPROVEMENT.md | AI performance monitoring framework |

---

*ClaudePrepFiles v2.1 — March 2026*
*Source: https://gitlab.com/smorente.syntax/claudeprepfiles*
