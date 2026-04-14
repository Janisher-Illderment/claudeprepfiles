# Quick Start Guide
## Get Professional Claude Code Configuration in 5 Minutes

---

## The 4 Steps

### Step 1 — Clone (1 minute)
```bash
git clone https://github.com/Janisher-Illderment/claudeprepfiles.git
cd claudeprepfiles
```

### Step 2 — Install Global Config (2 minutes)
```bash
# Create Claude config directory if it doesn't exist
mkdir -p ~/.claude

# Install the global config
cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md

# Verify it's there
ls -la ~/.claude/CLAUDE.md
```

### Step 3 — Verify It's Working (1 minute)
Open Claude Code and run this exact prompt:
```
What are your core operating principles for this session?
```

**You should see Claude mention:**
- Marking `[UNCERTAIN]` for things it's not sure about
- Confirming before destructive operations
- Model selection awareness (Opus/Sonnet/Haiku)
- Minimum output / no padding

If Claude gives a generic answer about being helpful, the config isn't loading — check `INSTALLATION.md` troubleshooting section.

### Step 4 — Set Up Project Config (1 minute, optional but recommended)
```bash
# In any project you work on:
mkdir -p /path/to/your-project/.claude
cp project_CLAUDE_ENHANCED_TEMPLATE.md /path/to/your-project/.claude/CLAUDE.md

# Then open it and fill in the [CONFIGURE] sections
# Minimum useful sections: 1 (identity), 2 (architecture), 8 (gotchas)
```

---

## 5 Things You'll Notice Immediately

1. **Claude stops confidently making things up** — You'll see `[UNCERTAIN]` markers appear when Claude isn't sure about an API, file path, or fact.

2. **Destructive commands get a pause** — Before running `rm`, `git reset --hard`, or database drops, Claude will describe what will happen and ask to proceed.

3. **Responses are shorter and more direct** — No more "Certainly! I'd be happy to help you with that! Here's what I'll do first..." — just the answer.

4. **Model selection becomes explicit** — Claude will sometimes note when a task warrants deeper reasoning vs. quick execution.

5. **Code comes with its assumptions stated** — Instead of silently guessing your framework version, Claude says what it's assuming and marks uncertainty.

---

## 3 Model Selection Rules to Memorize

```
HARD PROBLEM / HIGH STAKES  →  Opus 4.6  (deep reasoning)
DAILY PROFESSIONAL WORK     →  Sonnet 4.6 (best balance)
SIMPLE / REPETITIVE         →  Haiku 4.5  (fast & cheap)
```

When in doubt: start with Sonnet. If the answer isn't good enough, try Opus.

---

## Budget Quick Reference

| Budget Rule | What It Means |
|-------------|---------------|
| 60/35/5 | 60% Haiku, 35% Sonnet, 5% Opus across your tasks |
| Free first | Try Haiku first — it handles more than you'd expect |
| Invest in Opus for | Architecture, security, debugging production issues |
| /effort low | Quick tasks, reduce reasoning cost |
| /effort max | Hard problems where thinking time is worth it |

---

## Safety Tips — 3 Things NOT to Do

1. **Don't use the global config as-is for a security-sensitive project** — Add project-specific security constraints in your project CLAUDE.md (Section 7 of the template).

2. **Don't skip the project config if working in a complex codebase** — The global config doesn't know your project's gotchas. 30 minutes filling in the template saves hours of Claude making wrong assumptions.

3. **Don't assume Opus is always better** — It's better for complex reasoning, but for formatting, boilerplate, or simple lookups, Haiku gives the same result at a fraction of the cost.

---

## What to Read Next

| Goal | Read |
|------|------|
| Understand all 14 power patterns | `global_CLAUDE_ENHANCED.md` Section 11 |
| Set up for your team | `INSTALLATION.md` → Team Setup |
| Understand March 2026 features | `MARCH_2026_UPDATE_SUMMARY.md` |
| Learn about AI Agent files | `claude_code_AGENT_PROTOCOL.md` |
| See all available files | `INDEX.md` |

---

*ClaudePrepFiles v2.3 — March 2026*
