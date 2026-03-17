# Upload Guide
## Deploying ClaudePrepFiles to Your Team's GitLab or GitHub

---

## Quick Upload (For Existing Git Users)

If you have a repository set up and just need to push these files:

```bash
# 1. Clone your target repo
git clone https://gitlab.com/your-org/your-repo.git
cd your-repo

# 2. Copy files from ClaudePrepFiles
cp -r /path/to/claudeprepfiles/* .

# 3. Stage and commit
git add .
git status  # verify what's being committed

git commit -m "feat: Add ClaudePrepFiles professional Claude Code configuration

- Add global_CLAUDE_ENHANCED.md v2.1 (12 sections, 14 power patterns)
- Add project_CLAUDE_ENHANCED_TEMPLATE.md with [CONFIGURE] placeholders
- Add comprehensive documentation (README, QUICK_START, INSTALLATION, etc.)
- Add March 2026 features: model selection, /effort, /loop, 1M context
- Add AI Agent self-governance files (3 files)
- MIT License"

# 4. Push
git push origin main

# 5. Verify — go to your repo in the browser and confirm files appear
```

---

## Manual Web Upload (For Non-Git Users)

If you prefer using the GitLab web interface:

### Using GitLab Web IDE

1. **Open your repository** in GitLab
2. Click the **"+"** button → **"New file"** (or use Web IDE)
3. For each file:
   - Enter the file name (e.g., `global_CLAUDE_ENHANCED.md`)
   - Paste the file contents
   - Click **"Commit changes"**
4. For `.gitignore` (hidden file): enter `.gitignore` as the filename — GitLab handles hidden files correctly

### Upload Multiple Files at Once
GitLab Web IDE supports drag-and-drop for multiple files:
1. Repository → **Web IDE** button
2. Drag all files from your local folder into the file tree
3. **Commit** all changes together with one commit message

### Verifying Each File
After upload, click on each file and verify:
- File renders as markdown (not raw text)
- Content looks correct (spot check a few lines)
- File size is reasonable (not 0 bytes, not unexpectedly huge)

---

## Team Deployment Patterns

### Pattern A: Central Team Fork
Best for teams wanting a shared, customizable version:

```bash
# 1. One person forks the repo (GitLab: Fork button)
# 2. Team lead customizes global_CLAUDE_ENHANCED.md with company standards
# 3. Team lead creates project configs for main projects
# 4. Share the fork URL with the team
# 5. Each developer clones the fork and runs the install script

# Install script (add to engineering onboarding):
curl -s https://gitlab.com/your-org/claudeprepfiles/-/raw/main/install.sh | bash
```

### Pattern B: Embedded in Monorepo
For teams with a central internal tooling repo:

```
internal-tools/
├─ claude-code/
│   ├─ global_CLAUDE_ENHANCED.md  ← Team's customized global config
│   ├─ project-configs/
│   │   ├─ api-service/CLAUDE.md
│   │   ├─ frontend/CLAUDE.md
│   │   └─ data-pipeline/CLAUDE.md
│   └─ README.md
└─ other-tools/
```

### Pattern C: GitLab Subgroup Template
For organizations using GitLab at scale:

1. Create a template project in your GitLab subgroup
2. Add ClaudePrepFiles as the base
3. Enable "Use as project template" in GitLab settings
4. New projects automatically include the Claude config

### Self-Hosted GitLab
Same process as cloud GitLab. The only difference is your remote URL:
```bash
git remote set-url origin https://your-gitlab.company.com/org/repo.git
```

---

## Verification Checklist

After upload, verify the following in your repository's web interface:

```
☐ All files present in the repo browser
☐ README.md renders as markdown (formatted, not raw text)
☐ global_CLAUDE_ENHANCED.md is present (most important file)
☐ project_CLAUDE_ENHANCED_TEMPLATE.md is present
☐ 3 AI Agent files are present (AGENT_PROTOCOL, AGENT_PATTERNS, AGENT_SELF_IMPROVEMENT)
☐ LICENSE file is present and correct
☐ .gitignore is present
☐ Git commit log shows your commit message
☐ File sizes are reasonable (global config should be ~40-50KB)
☐ No broken images or links in README (spot-check)
```

---

## Post-Upload Tasks

### Update Repository Description
In GitLab: Settings → General → Project description

Suggested description:
```
Professional Claude Code configuration system — global config, project template,
14 power patterns, March 2026 model selection strategy, and AI Agent
self-governance framework. MIT License.
```

### Add Topics/Tags
In GitLab: Settings → General → Topics

Suggested tags:
- `claude-code`
- `ai-assistant`
- `devops`
- `prompt-engineering`
- `developer-tools`
- `software-engineering`
- `configuration`

### Create a Release
Tag the upload as the v2.1 release:
```bash
git tag -a v2.1-march-2026 -m "March 2026 Edition

- 14 files for users
- 3 AI Agent self-governance files
- Complete model selection strategy
- March 2026 features documented"

git push origin v2.1-march-2026
```

Then create a Release in GitLab (Deployments → Releases → New release).

### Announce to Your Team

**Slack message template:**
```
📢 We now have a professional Claude Code configuration system.

Repository: [your repo URL]
Install: git clone [repo] && cp global_CLAUDE_ENHANCED.md ~/.claude/CLAUDE.md

What you get: consistent behavior, uncertainty markers, model selection
guidance, 14 power patterns, and per-project configs.

Start with README.md. Takes 5 minutes to install.
```

---

## Troubleshooting

### "Push rejected: protected branch"
```bash
# You may not have push access to main. Try:
git push origin main --force-with-lease
# If still rejected, check branch protection rules in GitLab Settings
```

### "Files not appearing in GitLab"
```bash
# Verify the push succeeded:
git log --oneline | head -3
git remote -v  # confirm remote is correct

# Sometimes the UI takes a moment to refresh — hard refresh the browser
```

### "README doesn't render as markdown"
- Verify the file is named exactly `README.md` (case-sensitive on Linux GitLab)
- Check there are no extra characters at the start of the file
- GitLab markdown rendering requires the file to be in the repo root or specified location

### "Merge conflict on push"
```bash
git pull origin main --rebase
# Resolve any conflicts
git rebase --continue
git push origin main
```

### "I uploaded the wrong version of a file"
```bash
# Option 1: Edit directly in GitLab web editor
# Option 2: Fix locally and push again
git add corrected-file.md
git commit -m "fix: correct [filename] content"
git push origin main
```

---

*ClaudePrepFiles v2.1 — March 2026*
