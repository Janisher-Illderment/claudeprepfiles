# Transition Guide: v1.0 → v2.1
## Upgrading Your Claude Code Configuration

---

## What's New in v2.1

Three major additions beyond v1.0:

**1. Model Selection Strategy (Section 9)**
A complete decision framework for Opus/Sonnet/Haiku — when to use each, with a decision flowchart, task type table, and the 60/35/5 budget rule. Essential now that Opus is the default model.

**2. Pro Resource Management (Section 10)**
How to use 1M context windows effectively, /effort levels, /loop scheduling, fast mode, compaction, and voice mode. All March 2026 features in one place.

**3. AI Agent Self-Governance Files (3 new files)**
A framework that applies the same professional standards to Claude's own operation. Read if you want to understand the "recursive excellence" concept.

---

## What Changed vs. v1.0

| Section | v1.0 | v2.1 |
|---------|------|------|
| Model selection | Not covered | Full strategy (Section 9) |
| Resource management | Basic | Comprehensive (Section 10) |
| Power patterns | 8 patterns | 14 patterns (Section 11) |
| AI Agent files | Not included | 3 new files |
| Hallucination guardrails | Basic mention | Strict protocol (Section 4) |
| DevOps guardrails | General | Specific destructive op protocol (Section 7) |
| Session checklist | Not included | Section 12 |

**No breaking changes** — v2.1 is a superset of v1.0. Existing configurations continue to work. New sections add capability without removing anything.

---

## 5-Step Migration Path

### Step 1: Back Up Your Existing Config
```bash
# Never overwrite without a backup
cp ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.backup-$(date +%Y%m%d)
echo "Backup created: ~/.claude/CLAUDE.md.backup-$(date +%Y%m%d)"
```

### Step 2: Install New Global Config
```bash
# Option A: Full replacement (recommended for most users)
cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md

# Option B: Merge (if you have significant custom additions)
# Open both files side by side and copy new sections into your existing config
# Sections 9, 10, and 11 are the most important to add
```

### Step 3: Update Model Selection Habits
The single most impactful change. Before v2.1, most users defaulted to whatever model was active. Now, be intentional:

```
Create a mental habit:
BEFORE each Claude Code task → ask "what model does this actually need?"
```

Quick reference card to memorize:
- Quick question / formatting → Haiku
- Daily coding work → Sonnet
- Architecture / debugging hard issues → Opus

### Step 4: Add Project Configs Where Missing
If you work in multiple projects, add `.claude/CLAUDE.md` to each one using the template. Priority order:
1. Projects with complex gotchas or many contributors
2. Projects you use Claude Code in daily
3. All other projects

### Step 5: Verify and Test
```bash
# Check the installed config version:
head -3 ~/.claude/CLAUDE.md

# Test the new behavior in Claude Code:
"What model would you recommend for reviewing a security-sensitive PR
and why?"
# Should reference Opus and explain the reasoning
```

---

## FAQ

### "Will this break my existing workflows?"
No. The new config adds guidance without removing what worked before. Your existing prompt patterns will continue to function. You'll notice Claude is more structured and explicit about uncertainty — that's the main behavioral change.

### "Do I have to change how I use Claude?"
Only if you want the benefits of Section 9 (model selection). The other changes are passive improvements. The 14 power patterns in Section 11 are opt-in — use the ones that work for your style.

### "What if I'm on a basic plan, not Max/Team?"
The model selection section is less relevant (you have limited model switching options). Focus on:
- Sections 1-8: Core operating principles (work on any plan)
- Section 11: Power patterns (all work on any plan)
- Project template: Per-project context (benefits all plans)

The model selection strategy still helps you make the most of whatever model access you have.

### "I don't do DevOps, is this still relevant?"
Yes. Section 7 (DevOps Guardrails) is the most specialized section. Everything else applies to:
- Web development
- Backend/API work
- Data engineering
- Mobile development
- Any professional software engineering

Skip Section 7 if it doesn't apply, or skim it — the destructive operation concepts are useful even outside pure DevOps.

### "Can I pick and choose sections?"
Absolutely. The config is modular. Comment out or delete sections that don't apply:

```markdown
<!-- Section 7: DevOps Guardrails — DISABLED (not applicable to this project) -->
```

Or just delete sections from your ~/.claude/CLAUDE.md entirely. The remaining sections still provide value independently.

### "What about the AI Agent files — do I need those?"
They're optional reading. The AI Agent files (AGENT_PROTOCOL.md, AGENT_PATTERNS.md, AGENT_SELF_IMPROVEMENT.md) describe how Claude should govern its own reasoning. Reading them gives you insight into how Claude operates and how to collaborate more effectively. They don't need to be installed anywhere — they're conceptual frameworks.

---

## Migration Scenarios

### Scenario 1: Solo Developer, Existing Basic Config

**Before**: Single CLAUDE.md with project description and basic instructions.

**Migration**:
1. Install new global config (full replacement)
2. Add project template for your main project
3. Fill in Section 8 (Gotchas) — this is where immediate value comes from
4. Start using the model selection guidance

**Time needed**: 30 minutes

---

### Scenario 2: Team Lead, Standardizing Across a Team

**Before**: Each developer using Claude Code independently, no shared standards.

**Migration**:
1. Fork this repo to your org
2. Customize global_CLAUDE_ENHANCED.md with company-specific standards (Sections 2, 3, 7)
3. Create project templates for each major project
4. Add an install script to engineering onboarding
5. Share the model selection strategy as a team agreement

**Time needed**: 2-4 hours for setup, then ongoing maintenance

---

### Scenario 3: DevOps Engineer

**Before**: No Claude Code config, or very basic prompt.

**Migration**:
1. Install global config (Section 7 DevOps Guardrails is especially relevant)
2. Add infrastructure-specific gotchas to project configs
3. Use /loop for CI monitoring
4. Apply the Reference + Verify pattern for infrastructure changes

**Time needed**: 45 minutes

---

### Scenario 4: Consultant Working Across Multiple Projects

**Before**: Reinventing context for each client project.

**Migration**:
1. Install global config
2. Maintain a project CLAUDE.md template for each client engagement
3. Update the template at start of each engagement (30 min investment that saves hours)
4. Use Prompt Archive pattern (Section 11, Pattern 14) to reuse effective prompts

**Time needed**: 1 hour initial + 30 min per new engagement

---

*ClaudePrepFiles v2.1 — March 2026*
