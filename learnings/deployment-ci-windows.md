# Deployment, CI/CD, Windows, Git & Local-LLM Ops

## Deployment (PaaS / Docker)

### Don't rely on platform `startCommand` to expand `$PORT` / `$VAR`
**Learning:** PaaS `startCommand` fields (e.g. `railway.toml`) often run in exec form with no shell, so `$PORT` is passed literally and the app crashes at startup.
**Why:** Exec-form launchers bypass the shell entirely — there's no `/bin/sh -c` to expand env vars.
**How to apply:** Read env vars in-app (`os.environ.get("PORT", 8000)`), or use a Dockerfile shell-form `CMD` (`CMD uvicorn app --host 0.0.0.0 --port ${PORT:-8000}`). Never count on the platform config file to expand `$VAR`.

### Verify the real deployed URL before hardcoding it in metadata
**Learning:** Shared-namespace PaaS (Render, Vercel, Netlify, Fly) may append a hash suffix when your subdomain is taken; hardcoding the guessed URL in `canonical`/`og:url`/`sitemap.xml`/`robots.txt` can point SEO at a different site.
**Why:** Subdomain namespaces are global and shared; common names are often squatted.
**How to apply:** Before embedding any PaaS URL, confirm it's your app: `curl -s https://<guess>.<platform>/health`. If the response shape is wrong, get the real assigned URL from the dashboard (often `<name>-<4chars>.onrender.com`).

### Use `COPY . .` + `.dockerignore` for Python images; avoid selective `COPY`
**Learning:** Selective `COPY` (only `pyproject.toml` + `src/`) silently omits files build backends need at install time (`README.md` for hatchling, `MANIFEST.in` for setuptools) → `OSError` in the `pip install` layer.
**Why:** Python build backends inspect the whole project dir for metadata, not just the source tree.
**How to apply:** `COPY . .` then install, and control image contents via `.dockerignore` (`__pycache__/`, `.git/`, `tests/`, `*.egg-info/`, `dist/`, `build/`).

### After two consecutive platform-side deploy failures, test on a second platform
**Learning:** Infra-level errors (network-config failures, healthcheck timeouts with no app log) are indistinguishable from app bugs until you run the same artifact elsewhere; more commits won't fix a platform-side issue.
**Why:** Platforms have their own silent config problems that surface as misleading app-layer errors.
**How to apply:** If deploy #2 on platform A fails and looks infra-related, run the same `Dockerfile` on platform B before writing more code. If B works, the bug is in A's config.

### Start the tunnel before the app when the app needs its own public URL at startup
**Learning:** Apps that bake their public URL into env (`APP_URL`, `ALLOWED_ORIGINS`, OIDC redirect) can't be reconfigured without a full restart; when that URL comes from an ephemeral tunnel, the tunnel must be up and its URL captured before the app launches.
**Why:** The tunnel URL is only known after the tunnel starts; an app that boots first with a localhost placeholder gets wrong redirects/CORS/email links and needs a disruptive teardown.
**How to apply:** Start the tunnel with a logfile, wait ~5s, extract the URL, then start the app with it. On Windows specifically, a hidden-window background process has no capturable stdout — pass `--logfile` and poll the log:
```powershell
cloudflared tunnel --url http://localhost:8080 --logfile C:\tmp\cf.log   # background
Select-String C:\tmp\cf.log -Pattern "https://[a-z0-9-]+\.trycloudflare\.com" | Select-Object -Last 1
```

## CI / CD

### Check CI status at the start of any session that will commit
**Learning:** Pre-existing CI failures are invisible until deploy time; committing on a broken pipeline lengthens the failure list and hides which commit caused the real regression.
**Why:** A trivial CI fix (missing test-job dependency) otherwise taints every later commit.
**How to apply:** Early in a session: `gh run list --repo <owner>/<repo> --limit 3`; if the latest is red, fix CI before new code.

### Include changelog updates in the same commit as the visible change
**Learning:** A `feat:`/`fix:` that changes user-visible behavior should stage its changelog entry atomically — not in a follow-up `docs:` commit.
**Why:** A separate changelog commit triggers a second CI run and creates a window where the changelog is wrong; it also doubles PR review noise.
**How to apply:** Before committing a user-visible change, add the `CHANGELOG.md`/`changelog.json` entry (and version bump) in the same `git add`. Skip for internal refactors, CI fixes, pure test additions, dependency bumps.

### Declare `permissions: contents: write` on GitHub Actions jobs that create releases
**Learning:** Jobs using `softprops/action-gh-release` (or similar) fail with "Resource not accessible by integration" without an explicit `permissions: contents: write`; the error doesn't point at YAML permissions.
**Why:** Actions defaults `GITHUB_TOKEN` to read-only on repos with default-restricted token settings; write to `contents` must be opted into.
**How to apply:**
```yaml
jobs:
  release:
    permissions: { contents: write }
    steps: [ { uses: softprops/action-gh-release@v2, with: { files: dist/** } } ]
```

## Windows / PowerShell

### Detect the active shell before giving CLI commands on Windows
**Learning:** A Windows dev may be in PowerShell, Git Bash, or CMD, and syntax differs sharply (e.g. `curl` is an `Invoke-WebRequest` alias in PowerShell); the wrong syntax fails immediately.
**Why:** There's no single "Windows shell" — quoting, command names, and paths differ across the three.
**How to apply:** Look for a `PS C:\>` prompt to confirm PowerShell; when unsure, give both labeled variants; default to PowerShell on Windows. For Python scripts, prefer `#!/usr/bin/env python` (not `python3`), ship a `.cmd` wrapper, and add the script dir to PATH via `[Environment]::SetEnvironmentVariable`.

### Use `$env:VAR` or absolute paths in PowerShell hooks/scripts — never `%VAR%`
**Learning:** In configs invoking `pwsh -File <path>`, `%USERPROFILE%`-style tokens aren't expanded; the literal `%USERPROFILE%` is passed as a path and rejected.
**Why:** `%VAR%` is a CMD feature; calling PowerShell directly with `-File` has no CMD interpreter in the chain.
**How to apply:** Use an absolute path, or portable form `pwsh -NoProfile -Command "& '$env:USERPROFILE\.config\script.ps1'"`. Never `pwsh -File "%USERPROFILE%\..."`.

### Kill dev-server processes via `Get-NetTCPConnection`, not `taskkill`/`pkill`
**Learning:** `taskkill /F /PID N` fails in Git Bash (it reads `/F` as path `F:/`); `pkill -f` is unreliable on Windows; parsing `netstat -ano` is brittle across locales.
**Why:** Git Bash converts leading `/` flags to drive paths; typed PowerShell objects avoid all text-parsing fragility.
**How to apply:**
```powershell
(Get-NetTCPConnection -LocalPort 8765 -ErrorAction SilentlyContinue).OwningProcess |
  ForEach-Object { Stop-Process -Id $_ -Force -ErrorAction SilentlyContinue }
```

### Put the whole Docker bin directory on PATH, not just `docker.exe`
**Learning:** Docker Desktop installs `docker.exe` and `docker-credential-desktop.exe` in the same dir; exposing only `docker.exe` breaks registry ops with `exec: "docker-credential-desktop": executable file not found in %PATH%`.
**Why:** The credential helper is a separate binary in that dir, not bundled into `docker.exe`.
**How to apply:** Add the directory: `$env:Path = "C:\Program Files\Docker\Docker\resources\bin;$env:Path"`. Never alias individual Docker binaries.

## Git / GitHub CLI

### On Git Bash (Windows), drop the leading slash from `gh api` paths
**Learning:** `gh api /repos/{owner}/{repo}/pages` fails on Git Bash because the leading `/` is converted to a Windows absolute path.
**Why:** POSIX-path conversion rewrites any `/`-leading argument that looks like a path.
**How to apply:** Use a relative endpoint: `gh api repos/{owner}/{repo}/pages`.

### After a rename-vs-modify merge, verify content at the renamed destination
**Learning:** If branch A renames a file and branch B modifies the original path, git may auto-apply B's change to the renamed destination; the result can compile and pass tests while holding the wrong merged content.
**Why:** Rename detection applies content changes to the best-match path — structurally fine, semantically wrong.
**How to apply:** `git diff --name-status <base>...<branch>` to spot renames; merge with `--no-commit`, inspect content at the new path, run tests, then commit. Clean up residual empty directories.

### Add AI-tooling runtime directories to `.gitignore` proactively
**Learning:** AI coding tools create runtime dirs in the repo (e.g. `.claude/agent-memory/`, `.claude/tmp/`) that aren't auto-ignored and will appear in `git status`.
**Why:** The tool doesn't edit the project's `.gitignore` on install, so these accumulate and risk accidental commits.
**How to apply:** When working in a repo that uses such tools, check `git status` and add the dirs to `.gitignore` immediately.

## Local-LLM operations

### Verify Ollama model tags on the registry before suggesting `ollama pull`
**Learning:** Ollama tags (sizes, quant suffixes) aren't predictable from family names; an invented tag yields "manifest unknown".
**Why:** Families don't share a uniform scheme (e.g. Llama 3.2 has 1B/3B; the 8B is Llama 3.1).
**How to apply:** Confirm the exact tag at `ollama.com/library/<model>` first; if unsure, recommend the bare name (`ollama pull llama3.1`) and let Ollama pick the default. Never invent size variants.

### Scope local-model delegation to token-cheap I/O; don't delegate complex summarization
**Learning:** Small quantized local models (7–9B, Q4) handle boilerplate and static-data formatting but hallucinate or ignore format constraints on complex summarization over real codebases (files >500 lines).
**Why:** A 7–9B Q4 model is ~GPT-3.5 class; it can't reliably follow formatting on large, semantically complex inputs.
**How to apply:** Safe to delegate: JSON/CSV/MD data entry, <50-line test scaffolds, changelog drafts from a provided diff summary. Don't delegate: summarizing large files, architecture, debugging. For large-file context, prefer targeted grep/extract over piping the whole file. (See also model-tier routing in [ai-collaboration.md](ai-collaboration.md).)
