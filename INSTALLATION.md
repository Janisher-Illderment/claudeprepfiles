# Installation Guide
## Complete Setup for Individuals, Teams, and CI/CD Systems

---

## Prerequisites

Before installing ClaudePrepFiles, ensure you have:

- **Claude Code** installed and working (`claude --version`)
- **Git** installed (`git --version`)
- **bash or zsh** shell (Linux/macOS) or Git Bash (Windows)
- Read access to `https://gitlab.com/smorente.syntax/claudeprepfiles`

---

## 1. Individual User Setup

### Quick Install (5 steps)
```bash
# 1. Clone the repo
git clone https://gitlab.com/smorente.syntax/claudeprepfiles.git ~/claudeprepfiles

# 2. Create Claude config directory
mkdir -p ~/.claude

# 3. Install global config
cp ~/claudeprepfiles/global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md

# 4. Verify install
cat ~/.claude/CLAUDE.md | head -5
# Should show: "# CLAUDE CODE PROFESSIONAL CONFIGURATION v2.1"

# 5. Test in Claude Code (open Claude Code and run):
# "What are your operating principles for this session?"
```

### Verification Test
After installation, open Claude Code and type:
```
List 3 things you will NOT do based on your current configuration.
```

Expected response should include: won't fabricate uncertain facts, won't run destructive operations without confirmation, won't produce unnecessarily padded responses.

### Keeping It Updated
```bash
# Pull latest version monthly:
cd ~/claudeprepfiles
git pull origin main

# Re-install if global_CLAUDE_ENHANCED.md changed:
cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md
```

---

## 2. Team Setup and Sharing

### Strategy: Shared Git Repository
The recommended approach for teams is to fork this repo and maintain your own version with team-specific customizations.

```bash
# 1. Fork on GitLab (via web interface)
# Your fork: https://gitlab.com/your-org/claudeprepfiles

# 2. Add team-specific sections to global_CLAUDE_ENHANCED.md
#    - Company coding standards
#    - Internal tool references
#    - Team-specific model selection rules

# 3. Share with the team:
# Option A: Each developer clones the fork and installs manually
# Option B: Add install script to your engineering onboarding docs
```

### Team Install Script
Create this as `install-claude-config.sh` in your onboarding scripts:
```bash
#!/bin/bash
set -e

REPO="https://gitlab.com/your-org/claudeprepfiles.git"
INSTALL_DIR="$HOME/.claudeprepfiles"

echo "Installing ClaudePrepFiles..."

# Clone or update
if [ -d "$INSTALL_DIR" ]; then
    echo "Updating existing installation..."
    git -C "$INSTALL_DIR" pull origin main
else
    echo "Fresh installation..."
    git clone "$REPO" "$INSTALL_DIR"
fi

# Install global config
mkdir -p ~/.claude
cp "$INSTALL_DIR/global_CLAUDE_ENHANCED.md" ~/.claude/CLAUDE.md

echo "✅ ClaudePrepFiles installed. Restart Claude Code to apply."
echo "   Config location: ~/.claude/CLAUDE.md"
```

### Per-Project Configuration
For each team project, add a project-specific CLAUDE.md:
```bash
# In the project repo root:
mkdir -p .claude
cp ~/claudeprepfiles/project_CLAUDE_ENHANCED_TEMPLATE.md .claude/CLAUDE.md

# Fill in project-specific context (team, architecture, gotchas)
# Commit this file to the repo — all team members benefit
git add .claude/CLAUDE.md
git commit -m "chore: add Claude Code project configuration"
```

---

## 3. CI/CD Integration

### GitHub Actions
Add Claude Code configuration to your CI pipeline:

```yaml
# .github/workflows/claude-config.yml
name: Claude Code Config

on:
  workflow_dispatch:  # Manual trigger
  push:
    paths:
      - '.claude/CLAUDE.md'

jobs:
  validate-config:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Validate CLAUDE.md exists
        run: |
          if [ ! -f ".claude/CLAUDE.md" ]; then
            echo "❌ Missing .claude/CLAUDE.md — add project config"
            exit 1
          fi
          echo "✅ CLAUDE.md present"

      - name: Check for [CONFIGURE] placeholders
        run: |
          if grep -q "\[CONFIGURE:" .claude/CLAUDE.md; then
            echo "⚠️ Warning: Unconfigured placeholders in CLAUDE.md:"
            grep -n "\[CONFIGURE:" .claude/CLAUDE.md
            # Don't fail — warn only
          else
            echo "✅ No unconfigured placeholders"
          fi
```

### GitLab CI
```yaml
# .gitlab-ci.yml — add to existing pipeline or create new

validate-claude-config:
  stage: lint
  image: alpine:latest
  before_script:
    - apk add --no-cache grep
  script:
    - |
      if [ ! -f ".claude/CLAUDE.md" ]; then
        echo "❌ Missing .claude/CLAUDE.md"
        exit 1
      fi
      echo "✅ CLAUDE.md present"
      # Check config version
      VERSION=$(grep "Version:" .claude/CLAUDE.md | head -1 | grep -oP '\d+\.\d+')
      echo "Config version: $VERSION"
  rules:
    - changes:
        - ".claude/CLAUDE.md"
  allow_failure: true  # Warn, don't block
```

### Jenkins
```groovy
// Jenkinsfile — add to existing pipeline

stage('Validate Claude Config') {
    steps {
        script {
            def claudeConfig = '.claude/CLAUDE.md'
            if (!fileExists(claudeConfig)) {
                echo "WARNING: No Claude Code config found at ${claudeConfig}"
                echo "Consider adding project configuration for better AI assistance"
            } else {
                echo "Claude Code config found"
                sh "head -3 ${claudeConfig}"
            }
        }
    }
}
```

---

## 4. Docker Setup

### Dockerfile Integration
Include Claude configuration in your development Docker image:

```dockerfile
# In your dev Dockerfile or docker-compose override

FROM your-base-image

# Install Claude Code config for container-based development
RUN mkdir -p /root/.claude
COPY .devcontainer/claude-config/CLAUDE.md /root/.claude/CLAUDE.md

# Or fetch from your team's repo:
# RUN git clone --depth 1 https://gitlab.com/your-org/claudeprepfiles.git /tmp/cp && \
#     cp /tmp/cp/global_CLAUDE_ENHANCED.md /root/.claude/CLAUDE.md && \
#     rm -rf /tmp/cp
```

### docker-compose for Dev Environments
```yaml
# docker-compose.override.yml (dev only, not committed to main config)
version: '3.8'

services:
  app:
    volumes:
      # Mount your Claude config into the container
      - ~/.claude:/root/.claude:ro
      # Project-specific config
      - ./.claude:/workspace/.claude:ro
```

### VS Code Dev Container
```json
// .devcontainer/devcontainer.json
{
  "name": "My Project",
  "dockerComposeFile": "../docker-compose.yml",
  "service": "app",
  "postCreateCommand": "mkdir -p ~/.claude && cp .devcontainer/CLAUDE.md ~/.claude/CLAUDE.md",
  "customizations": {
    "vscode": {
      "extensions": ["anthropic.claude-code"]
    }
  }
}
```

---

## 5. Updating

### Check Current Version
```bash
# In your installed ClaudePrepFiles:
head -3 ~/.claude/CLAUDE.md
# Should show version line
```

### Update Process
```bash
# 1. Pull latest
cd ~/claudeprepfiles
git fetch origin
git log --oneline HEAD..origin/main  # See what changed

# 2. Review changes before applying
git diff HEAD origin/main -- global_CLAUDE_ENHANCED.md

# 3. Pull if satisfied
git pull origin main

# 4. Re-install global config (if it changed)
cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md

# 5. Update project configs if template changed significantly
# (manual process — check TRANSITION_GUIDE.md)
```

---

## 6. Verification Checklist

After installation, verify:

```
☐ ~/.claude/CLAUDE.md exists and contains v2.1 content
☐ Claude Code reads the config (run verification test from QUICK_START.md)
☐ Claude uses [UNCERTAIN] markers appropriately
☐ Claude asks to confirm before destructive operations
☐ Project config exists in .claude/CLAUDE.md (if using project config)
☐ Project config has [CONFIGURE] placeholders filled in
☐ Team members all have the same version installed
```

---

## 7. Troubleshooting

### Claude isn't reading CLAUDE.md
```bash
# Check the file exists:
ls -la ~/.claude/CLAUDE.md

# Check Claude Code version (needs recent version for CLAUDE.md support):
claude --version

# Check for syntax errors in the file:
cat ~/.claude/CLAUDE.md | head -20
# Should start with "# CLAUDE CODE PROFESSIONAL CONFIGURATION"
```

### Config seems to be ignored
- Claude Code reads `~/.claude/CLAUDE.md` as the global config
- Project config: Claude Code looks for `CLAUDE.md` in the current directory and parent directories
- Try restarting Claude Code after changing config files
- Confirm you're running Claude Code (not claude.ai web) — web version doesn't read local files

### Wrong model being selected
- Claude Code selects model based on your plan settings, not the CLAUDE.md
- The CLAUDE.md model selection section is **guidance for users**, not automatic model switching
- Use `/model` command in Claude Code to switch models manually
- The config helps you know *when* to switch, not switching automatically

### Context window issues
- If Claude loses context mid-conversation, use `/compact` to compress history
- For very long sessions, periodically save key decisions to a `CONTEXT.md` file
- The project CLAUDE.md should contain the most critical context that must always be available

### Token budget problems
- Check which model is active — if Opus is default and you're doing simple tasks, switch to Haiku
- Use `/effort low` for tasks that don't need deep reasoning
- Batch similar tasks together instead of submitting them one at a time

### Project config not being read
```bash
# Verify file location:
ls -la .claude/CLAUDE.md
# OR
ls -la CLAUDE.md

# Claude Code searches from current directory upward
# Make sure you're running Claude Code from within the project directory
```

### [CONFIGURE] placeholders causing issues
- Claude Code will try to interpret `[CONFIGURE: ...]` as instructions
- Fill in ALL placeholder sections before committing project config
- Or delete sections that don't apply rather than leaving them as templates

---

*ClaudePrepFiles v2.1 — March 2026*
*Source: https://gitlab.com/smorente.syntax/claudeprepfiles*
