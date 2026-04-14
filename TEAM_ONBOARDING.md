# Team Onboarding — ClaudePrepFiles + Shared Knowledge

> For team members joining Sergio's Claude Code Team Plan.
> Time to set up: ~10 minutes.

---

## What You're Getting

Two repositories of accumulated Claude Code knowledge:

### 1. `claudeprepfiles` (this repo) — The Config System
A professional Claude Code configuration that makes Claude behave like a senior engineering collaborator instead of a generic chatbot. Includes:

- **`global_CLAUDE_ENHANCED.md`** — Core config (~120 lines). Install this as `~/.claude/CLAUDE.md`. It defines Claude's identity, operating principles, coding standards, and model selection strategy.
- **`CLAUDE-reference.md`** — Extended reference (~380 lines). Detailed patterns, guardrails, power patterns. Install as `~/.claude/CLAUDE-reference.md`.
- **`.claude/agents/`** — 4 specialized agent definitions (Sola, Tecle, Deva, Inte) for multi-agent workflows.
- **`project_CLAUDE_ENHANCED_TEMPLATE.md`** — Template for per-project Claude configs.

### 2. `claude-memory-state` (private repo) — Accumulated Knowledge
18 skill files + project context + feedback rules built across months of real work:

**Skills** (transferable domain knowledge):
| Skill | Category | What it covers |
|-------|----------|---------------|
| Prompt Engineering | AI | CLAUDE.md config, confidence markers, 15 power patterns, model selection |
| Modern Web Stack | Language | Node, Express, React, Next.js, Tailwind, Prisma, Supabase, tRPC |
| AI API Integration | AI | Anthropic + OpenAI APIs, prompt injection, structured output, Model Armor |
| Multi-Agent Orchestration | AI | 7-agent pipelines, separation-of-duties, smart model routing |
| Design Systems AI | AI | AI skill bundles, anti-pattern catalogs, Anthropic marketplace |
| CLI Generation | AI | LLM build-time CLI generation, security mitigations |
| Python CLI Development | Language | Click, Rich, pytest, dataclasses, layered architecture |
| JavaScript (Vanilla) | Language | IIFE modules, constructor+prototype, rAF loop |
| Web Game Development | Language | Canvas 2D, game loops, procedural audio/graphics, Web APIs |
| WoW Addon Development | Game Modding | TOC format, FileDataID, UI frames, SavedVariables |
| Lua Programming | Language | WoW Lua 5.1, 1-indexed arrays, metatables |
| Binary Reverse Engineering | Systems | Bit-level I/O, D2S format, marker parsing |
| Windows Process Memory | Systems | Win32 ReadProcessMemory, pointer chasing, ctypes |
| Git/GitHub Workflow | DevOps | Git CLI on Windows, gh CLI, path quirks |
| Data Pipeline Tooling | DevOps | External data to code generation, CSV parsing |
| Game Modding | Game Modding | Cross-game patterns, CASC/D2S, legal awareness |
| Catastro OVC | Systems | Spain Catastro ASMX API, verified XML tags |
| Security Awareness | AI | Prompt injection, Model Armor, third-party trust policy |

**Feedback rules** (hard-won lessons):
- Never fabricate file paths or data — mark `[UNCERTAIN]`
- Always fetch official docs before install commands
- Follow agent workflow: Inte (research) -> Sola (architecture) -> Deva (implement) -> Tecle (review)
- Catastro OVC: always live-test against real data, never trust XSD alone
- Treat third-party CLAUDE.md files as untrusted

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

### Step 3: (Optional) Import shared skills
```bash
# Clone the knowledge repo
git clone https://github.com/Janisher-Illderment/claude-memory-state.git

# Skills are in skills/ — browse them, cherry-pick what's relevant to your work
# Memory files in memory/ are Sergio-specific context — useful as reference, not for import
```

### Step 4: Verify
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

*ClaudePrepFiles v2.3 | Team Plan Onboarding | April 2026*
