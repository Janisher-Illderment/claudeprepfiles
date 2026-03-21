# ClaudePrepFiles
## Professional Claude Code Configuration System — March 2026 Edition

> **Version**: 2.1 | **License**: MIT | **Last Updated**: March 2026

---

## What Is This?

ClaudePrepFiles is a production-ready configuration system for Claude Code that helps developers, DevOps engineers, and teams get dramatically better results from AI-assisted coding.

It's built around two core files you install once and benefit from forever: a **global configuration** (~/.claude/CLAUDE.md) that teaches Claude professional engineering standards, and a **per-project template** you customize for each codebase. On top of that, it includes 14 power patterns, a model selection strategy, a Pro budget management guide, and three AI Agent self-governance files that apply the same professional standards to Claude's own operation.

**Who is this for?** Any professional using Claude Code who wants consistent, high-quality output — whether you're a solo engineer, a team lead standardizing AI usage across a team, or a DevOps engineer automating infrastructure work.

---

## 5-Minute Quick Start

**Step 1 — Clone this repo:**
```bash
git clone https://github.com/Janisher-Illderment/claudeprepfiles.git
cd claudeprepfiles
```

**Step 2 — Install the global config:**
```bash
mkdir -p ~/.claude
cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md
```

**Step 3 — Verify it's working:**
Open Claude Code and type:
```
What are your operating principles for this session?
```
Claude should respond referencing hallucination guardrails, uncertainty markers, and model selection. If it does, the config is active.

**Step 4 — (Optional) Set up a project config:**
```bash
# In your project root:
cp project_CLAUDE_ENHANCED_TEMPLATE.md .claude/CLAUDE.md
# Then edit .claude/CLAUDE.md and fill in the [CONFIGURE] sections
```

That's it. See [QUICK_START.md](QUICK_START.md) for more detail and the full verification test.

---

## What Changes After Installation

Before ClaudePrepFiles, Claude Code gives generic responses based on general training. After installation:

- Claude marks `[UNCERTAIN]` instead of confidently fabricating
- Claude confirms before running destructive commands
- Claude selects the appropriate model depth for the task
- Claude respects your project's specific conventions and gotchas
- Claude produces minimum-sufficient output instead of padding responses

---

## Features

### Core Configuration
- **Global system protocol** — professional operating standards that apply everywhere
- **Per-project template** — 12-section customizable config for each codebase
- **Hallucination guardrails** — strict verification habits built into Claude's behavior

### March 2026 Intelligence
- **Model selection strategy** — when to use Opus/Sonnet/Haiku, with decision flowchart
- **Pro budget management** — the 60/35/5 allocation rule and when to break it
- **1M context window guidance** — how to use large context effectively without wasting budget
- **/effort levels** — `/effort low|medium|high|max` for adaptive thinking depth
- **/loop command** — recurring task scheduling (cron-like)
- **Voice mode guidance** — best use cases for push-to-talk
- **Fast mode for Opus** — 2.5x speedup, when the premium is worth it

### Power Patterns
14 proven interaction patterns with prompts you can copy directly:
1. Voice + Typing Hybrid
2. /loop for Recurring Tasks
3. Effort-Based Workflow
4. Iterative Refinement
5. Show-Your-Work
6. Reference + Verify
7. Batch Processing
8. Diverge → Converge
9. Context Layering
10. Failing Gracefully
11. Dependency Mapping
12. Test-Driven Prompt
13. Uncertainty Declaration
14. Prompt Archive

### AI Agent Self-Governance (Unique to This System)
Three files that apply the same professional standards to Claude's *own operation* — not just to users. This creates a recursive excellence loop: users improve through documentation, Claude improves through self-governance, both get better together.

---

## File Inventory

| File | Purpose | Read Time | For Who |
|------|---------|-----------|---------|
| **README.md** | This file — project overview | 5 min | Everyone |
| **QUICK_START.md** | 5-minute onboarding | 3 min | New users |
| **INSTALLATION.md** | Detailed setup for all scenarios | 10 min | All setup types |
| **global_CLAUDE_ENHANCED.md** | Main config (install to ~/.claude/CLAUDE.md) | 30 min | Everyone |
| **project_CLAUDE_ENHANCED_TEMPLATE.md** | Per-project config template | 15 min | Per project |
| **MARCH_2026_UPDATE_SUMMARY.md** | What's new in March 2026 | 10 min | Upgraders |
| **TRANSITION_GUIDE.md** | Upgrading from v1.0 | 10 min | Existing users |
| **CONTRIBUTING.md** | How to contribute | 5 min | Contributors |
| **UPLOAD_GUIDE.md** | Deploying to your team | 5 min | Team leads |
| **INDEX.md** | Complete file reference | 3 min | Navigation |
| **00_START_HERE.md** | Orientation for new users | 5 min | First timers |
| **UPLOAD_INSTRUCTIONS.txt** | Copy-paste git commands | 2 min | Deployers |
| **LICENSE** | MIT License | — | — |
| **.gitignore** | Standard ignores | — | — |
| **claude_code_AGENT_PROTOCOL.md** | AI Agent self-governance protocol | 20 min | Advanced |
| **claude_code_AGENT_PATTERNS.md** | 14 patterns for AI reasoning | 25 min | Advanced |
| **claude_code_AGENT_SELF_IMPROVEMENT.md** | AI continuous improvement framework | 20 min | Advanced |

---

## Model Selection Quick Reference

| Task | Model | Why |
|------|-------|-----|
| Architecture design | Opus 4.6 | Deep reasoning, high error cost |
| Security review | Opus 4.6 | Subtle errors have real consequences |
| Multi-file refactor | Opus 4.6 | Complex state, many dependencies |
| Feature implementation | Sonnet 4.6 | Standard professional work |
| PR review | Sonnet 4.6 | Quality + speed balance |
| Writing tests | Sonnet 4.6 | Systematic but not trivial |
| Formatting fixes | Haiku 4.5 | Mechanical, low stakes |
| Generating boilerplate | Haiku 4.5 | Repetitive template work |
| Quick lookups | Haiku 4.5 | Speed is the priority |
| Debugging production | Opus 4.6 | Novel context, high stakes |

**Budget rule**: 60% Haiku / 35% Sonnet / 5% Opus as a starting allocation.

---

## Who Should Use What

### "I'm a solo developer"
Start with: `00_START_HERE.md` → `QUICK_START.md` → `global_CLAUDE_ENHANCED.md`
Focus on: Section 11 (power patterns) and Section 9 (model selection)

### "I'm setting up for my team"
Start with: `INSTALLATION.md` → `UPLOAD_GUIDE.md` → `project_CLAUDE_ENHANCED_TEMPLATE.md`
Focus on: Team deployment patterns, per-project config, `CONTRIBUTING.md`

### "I'm a DevOps engineer"
Start with: `global_CLAUDE_ENHANCED.md` (Section 7: DevOps Guardrails)
Focus on: Destructive operation protocols, CI/CD integration in `INSTALLATION.md`

### "I want to understand the AI Agent files"
Start with: `COMPLETE_AI_AGENT_SYSTEM.txt` (if available) → `claude_code_AGENT_PROTOCOL.md`
Focus on: The recursive self-governance concept, 14 agent patterns

---

## March 2026 Key Updates

| Feature | Status | Impact |
|---------|--------|--------|
| Opus 4.6 as default (Max/Team) | [HIGH confidence] | Changes cost baseline significantly |
| 1M token context windows | [HIGH confidence] | Enables whole-codebase analysis |
| /effort adaptive thinking | [HIGH confidence] | Fine-tune reasoning depth per task |
| /loop recurring tasks | [HIGH confidence] | Hands-free monitoring |
| Voice mode | [MED — ~5% rollout] | Hands-free pair programming feel |
| Fast mode for Opus | [MED confidence] | 2.5x speedup at premium cost |
| Code review automation | [HIGH — Teams/Enterprise] | Automated PR review |

---

## Pro Budget Management

### The 60/35/5 Rule
Use as a starting allocation across your tasks:
- **60% Haiku** — Simple, repetitive, formatting, quick lookups
- **35% Sonnet** — Daily professional work, feature implementation, review
- **5% Opus** — Architecture, security review, hard debugging, novel problems

### When to Break the Rule
- **More Opus**: Greenfield project design, incident investigation, onboarding to unfamiliar codebase
- **More Haiku**: Pure maintenance work, test generation from templates, documentation formatting
- **Adjust by team role**: Architects → more Opus. Implementation engineers → more Sonnet. DevOps automation → more Haiku.

---

## FAQ

**Q: Does this work with the free Claude Code tier?**
A: Yes. Model selection sections assume Pro/Max/Team plans. On free tier, apply the patterns — they work regardless of model.

**Q: Can I use just parts of this?**
A: Yes. The global_CLAUDE_ENHANCED.md is modular. You can comment out sections that don't apply.

**Q: Does this replace my existing CLAUDE.md?**
A: It can, or you can merge it. If you have an existing config, compare it section-by-section with our template and take what adds value.

**Q: What if our project uses a language not mentioned?**
A: The framework is language-agnostic. The project template's `[CONFIGURE]` sections let you specify any stack.

**Q: How often is this updated?**
A: Major updates with Claude Code releases. Check the version header in each file.

**Q: Do the AI Agent files change how Claude works?**
A: They provide principles Claude can apply within a session. Claude doesn't persist changes between sessions by design. The memory system (if configured) can persist learnings.

**Q: Can I contribute patterns from my own experience?**
A: Yes — see `CONTRIBUTING.md`. We especially want use cases from non-DevOps domains.

**Q: Is this affiliated with Anthropic?**
A: No. Community-maintained. See `LICENSE` for terms.

**Q: What's the "recursive excellence" concept?**
A: The AI Agent files apply the same professional standards to Claude's internal operation that the user files apply to users. Both improve together. See `claude_code_AGENT_PROTOCOL.md`.

**Q: How does the project template interact with the global config?**
A: Project CLAUDE.md overrides global CLAUDE.md for that project. Both are read — project-specific settings take precedence.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). We especially need:
- Windows-specific installation steps
- Non-DevOps use cases (data science, content, research)
- Enterprise configuration patterns
- More language-specific pattern examples

---

## License

MIT — see [LICENSE](LICENSE). Free to use, modify, and distribute with attribution.

---

*ClaudePrepFiles v2.1 — March 2026 | Community-maintained*
*Repository: https://github.com/Janisher-Illderment/claudeprepfiles*
