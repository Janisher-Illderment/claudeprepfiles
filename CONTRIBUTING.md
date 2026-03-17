# Contributing to ClaudePrepFiles
## How to Help Improve This Resource

Thank you for considering a contribution. ClaudePrepFiles is community-maintained and improves through the collective experience of developers using Claude Code in real projects.

---

## Why Contribute?

Claude Code is evolving rapidly. Keeping this configuration system accurate and useful requires people who are actually using it in production to:
- Report when documented features behave differently than described
- Add patterns that work well in their context
- Correct outdated information as Claude Code updates
- Add use cases for domains not well-represented (non-DevOps, non-backend)

A well-maintained ClaudePrepFiles benefits everyone who uses Claude Code professionally.

---

## Ways to Contribute

### 1. Report Issues
The most valuable contribution is accurate bug reports. Open a GitLab issue with:
- Which file and section has the problem
- What the file currently says
- What the correct behavior/information is
- Confidence level: are you certain, or is this also worth verifying?

**Issue template:**
```
File: [e.g., global_CLAUDE_ENHANCED.md Section 9]
Problem: [what's wrong or outdated]
Evidence: [what you observed, link to official docs if available]
Confidence: [HIGH/MED/LOW]
Suggested fix: [what it should say]
```

### 2. Suggest Improvements
Open an issue with the `enhancement` label:
- New patterns you've found effective
- Sections that would benefit from more detail
- Examples from your domain that others would find useful

### 3. Submit Pattern Updates
Claude Code evolves. Patterns that worked with previous versions may be suboptimal now. If you have a better approach:
1. Explain what the current pattern does
2. Explain what your version does differently
3. Provide a concrete before/after example
4. Mark your confidence level

### 4. Add Use-Case Examples
We have good DevOps and backend examples. We need more from:
- Data science / ML workflows
- Frontend development
- Mobile development
- Technical writing
- Research and analysis

### 5. Fact-Check and Update
As the most time-sensitive contribution: when Claude Code updates and documented behavior changes, open a PR updating the relevant sections with correct information and updated confidence levels.

### 6. Translation (Future)
The project is English-only currently. If you're interested in translating to another language, open an issue to discuss the approach before starting.

---

## Submission Process

### Step 1: Fork the Repository
```bash
# Fork via GitLab web interface, then:
git clone https://gitlab.com/your-username/claudeprepfiles.git
cd claudeprepfiles
git remote add upstream https://gitlab.com/smorente.syntax/claudeprepfiles.git
```

### Step 2: Create a Feature Branch
```bash
git checkout -b fix/section-9-opus-default-behavior
# or
git checkout -b feat/add-data-science-patterns
# or
git checkout -b docs/update-march-2026-voice-mode
```

### Step 3: Make Changes Following the Style Guide

**Writing style:**
- Direct and actionable — say what to do, not just what not to do
- Use real examples, not abstract descriptions
- Keep sections self-contained — readers may start anywhere
- Bullet points for options, numbered lists for sequences

**Fact-checking standard:**
Mark ALL factual claims with confidence levels:
```
✅ Good: "Opus 4.6 is the default for Max plan users. [HIGH]"
❌ Bad: "Opus 4.6 is definitely the best model for all tasks."

✅ Good: "Voice mode is available to approximately 5% of users as of March 2026. [MED]"
❌ Bad: "Voice mode is available to everyone."
```

**Code examples:**
- All code examples must be runnable/copy-pasteable
- Specify the OS/environment if platform-specific
- Use comments to explain non-obvious parts

### Step 4: Fact-Check Your Additions
Before submitting, verify:
- [ ] Any factual claims are backed by official docs or your direct experience
- [ ] Confidence markers are applied to all factual claims
- [ ] Code examples have been tested (not just typed from memory)
- [ ] No `[CONFIGURE]` placeholders are left in template additions

### Step 5: Submit Merge Request
```bash
git add .
git commit -m "type(scope): description

Body: what changed and why
Confidence: [HIGH/MED/LOW] for any factual claims

Co-authored-by: [your name] <your@email.com>"

git push origin your-branch-name
```

Then open a Merge Request on GitLab. Include in the MR description:
- What you changed and why
- How you verified it's correct
- Any caveats or things reviewers should check

---

## Contribution Standards

### What Gets Accepted

✅ Corrections to inaccurate or outdated information (with evidence)
✅ New patterns that are proven effective (with examples)
✅ Better examples for existing patterns
✅ New domain coverage (data science, frontend, mobile, etc.)
✅ Improved explanations of existing concepts
✅ Fixed typos, grammar, broken formatting

### What Gets Rejected

❌ Unverified claims presented as fact
❌ Additions without confidence markers on factual claims
❌ Promotional content for tools/services
❌ Changes that reduce clarity or add unnecessary complexity
❌ Content generated by AI without human verification (ironic but important — this doc is about using AI well, not outsourcing quality control to it)

### Review Timeline

- Simple corrections (typos, broken links): 1-3 days
- Pattern additions or updates: 1-2 weeks
- Major structural changes: 2-4 weeks (may require discussion first)

If your MR sits without response for more than 2 weeks, ping in the GitLab comments.

---

## Areas Especially Needing Help

Current gaps in the documentation:

**Windows Users**
- INSTALLATION.md assumes bash/macOS/Linux
- Windows-specific installation steps needed
- Git Bash vs PowerShell vs WSL considerations

**Non-DevOps Domains**
- Data science / Jupyter workflows
- Frontend (React/Vue/Angular specific patterns)
- Mobile development (iOS/Android)
- Technical writing and documentation work
- Research and analysis workflows

**Enterprise Patterns**
- SSO/SAML integration context
- Air-gapped environment considerations
- Compliance-focused project configuration examples

**Non-English Documentation**
- Spanish, French, German, Portuguese translations would significantly expand reach

---

## Recognition

Contributors are credited in:
- The commit history (permanent)
- A CONTRIBUTORS section (added when we reach 10+ contributors)

All contributions are MIT licensed — by submitting, you agree your contribution can be freely used under the project's MIT license.

---

## Code of Conduct

This project follows a simple standard: be professional and direct. We welcome:
- Factual corrections even if they challenge existing content
- Dissenting views when backed by evidence
- Questions from people learning

We do not welcome personal attacks, off-topic discussion, or promotional content.

---

*ClaudePrepFiles v2.1 — March 2026*
*Source: https://gitlab.com/smorente.syntax/claudeprepfiles*
