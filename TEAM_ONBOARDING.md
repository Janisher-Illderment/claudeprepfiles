# Team Onboarding — ClaudePrepFiles

> For team members joining a Claude Code Team Plan.
> Time to set up: ~10 minutes.

---

## What You're Getting

A professional Claude Code configuration that makes Claude behave like a senior engineering collaborator instead of a generic chatbot. Includes:

- **`global_CLAUDE_ENHANCED.md`** — Core config (~120 lines). Install as `~/.claude/CLAUDE.md`. Defines Claude's identity, operating principles, coding standards, and model selection strategy.
- **`CLAUDE-reference.md`** — Extended reference (~380 lines). Detailed patterns, guardrails, 15 power patterns. Install as `~/.claude/CLAUDE-reference.md`.
- **`.claude/agents/`** — 4 specialized agent definitions (Sola, Tecle, Deva, Inte) for multi-agent workflows.
- **`project_CLAUDE_ENHANCED_TEMPLATE.md`** — Template for per-project Claude configs.

---

## Quick Setup (10 minutes)

### Step 1: Install the core config
```bash
# Clone this repo
git clone https://github.com/Janisher-Illderment/claudeprepfiles.git

# Install the core config
cp claudeprepfiles/global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md
cp claudeprepfiles/CLAUDE-reference.md ~/.claude/CLAUDE-reference.md

# IMPORTANT: Edit Section 0 in ~/.claude/CLAUDE.md to match YOUR environment
# (OS, shell, browser, admin permissions, etc.)
```

### Step 2: Install agent definitions
```bash
mkdir -p ~/.claude/agents
cp claudeprepfiles/.claude/agents/*.md ~/.claude/agents/
```

### Step 3: Verify
Open Claude Code and ask: "What version of CLAUDE.md is loaded?" It should say v2.3.

---

## The 4 Agents — When to Use Each

| Agent | Model | Use For | Trigger |
|-------|-------|---------|---------|
| **Inte** (Researcher) | Haiku | Doc lookup, web search, fact-finding | Before any external API work |
| **Sola** (Solution Architect) | Opus | Architecture design, ADRs, cost estimates | Before building a new system |
| **Deva** (Full-Stack Dev) | Opus | Implementation, debugging, code review | Any non-trivial coding task |
| **Tecle** (Tech Lead) | Opus | Code review, vulnerability scan, quality | Before merging features |

**Correct workflow:** `Inte -> (Sola if architecture) -> Deva -> Tecle -> commit`

---

## Key Concepts to Know

### Confidence Markers
Claude will tag claims with `[HIGH]`, `[MED]`, `[LOW]`, `[UNCERTAIN]`, or `[ERROR]`. Trust accordingly.

### Model Selection
```
Opus 4.6  — Architecture, security review, deep debugging, novel problems
Sonnet 4.6 — Feature implementation, PR review, tests, docs
Haiku 4.5  — Formatting, rename, boilerplate, quick lookups
```
Default budget: 60% Haiku / 35% Sonnet / 5% Opus.

### Conventional Commits
All commits follow: `feat:`, `fix:`, `refactor:`, `test:`, `docs:` — never freeform.

### What to Customize

After installing, **you must edit Section 0** of `~/.claude/CLAUDE.md` to match your own environment:
- Your OS and locale
- Your shell (Bash, Zsh, PowerShell)
- Your browser (for OAuth flows)
- Your admin permission level
- Any tools you know are/aren't installed

Everything else works out of the box.

---

## Bootstrap Prompt

Paste the following into your first Claude Code session after setup to verify everything works:

```
I've just installed the ClaudePrepFiles v2.3 configuration system. Please:
1. Confirm you can see the CLAUDE.md config (what version?)
2. List the agents available in ~/.claude/agents/
3. Show me a quick summary of the model selection strategy
4. Create a test memory entry to verify the auto-memory system works

This is a verification session — just confirm the setup is working.
```

---

## Further Reading

| File | When to read it |
|------|----------------|
| [00_START_HERE.md](00_START_HERE.md) | First-time orientation |
| [QUICK_START.md](QUICK_START.md) | 5-minute install (alternative to this guide) |
| [INSTALLATION.md](INSTALLATION.md) | Advanced setup (CI/CD, Docker, team scripts) |
| [INDEX.md](INDEX.md) | Full file reference |
| [claude_code_AGENT_PROTOCOL.md](claude_code_AGENT_PROTOCOL.md) | How Claude governs itself |
| [claude_code_AGENT_PATTERNS.md](claude_code_AGENT_PATTERNS.md) | 14 reasoning patterns |

---

*ClaudePrepFiles v2.3 | Team Onboarding | April 2026*
