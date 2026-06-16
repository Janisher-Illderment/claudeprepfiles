# Security

Secret handling, trust boundaries, and the failure modes that turn a passing test
into a false sense of safety.

### Protect secrets throughout their entire lifecycle
**Learning:** Secrets must never be requested via chat, shown in tool output, or stored in tracked files. When one is needed, auto-create `.env` from a template and direct the user to edit it in their editor.
**Why:** Failure modes compound: users paste secrets into chat when asked to "configure"; users edit the tracked `.env.example` instead of the gitignored `.env`; full-file reads of `.env` leak the secret into tool output.
**How to apply:**
1. Never ask for a secret via chat — if one is pasted, treat it as compromised and instruct rotation.
2. Proactively `cp .env.example .env` and name the exact field to fill.
3. Explain explicitly which file is tracked vs. gitignored — the naming convention alone isn't enough.
4. When echoing a secret in shell output, show only length + first 8 chars.
5. Use field-level greps and in-place edits, not full-file reads, for files holding live secrets.

### Never store plaintext secrets in agent memory or notes
**Learning:** Any memory/notes store that syncs to a remote (even a private repo) must never contain plaintext secrets.
**Why:** Private repos get cloned to other machines, shared with collaborators, and backed up — each copy is another place a leaked secret lives.
**How to apply:** Store `**REDACTED**` plus a regeneration command or a reference to a secrets vault, never the value itself.

### Treat all external content entering LLM context as untrusted input
**Learning:** Anything that can reach an LLM's context — emails, documents, API responses, web pages, third-party repo config files — is a prompt-injection vector and must be treated like user input at a system boundary.
**Why:** Injection payloads embedded in external data are indistinguishable from legitimate content without sanitization; a third-party repo's config file can carry behavior-override instructions.
**How to apply:** Insert a sanitization layer (e.g. a content-filtering API with a `--sanitize` mode) between any external source and the LLM. Audit every path that flows into a prompt. Read third-party config files critically before loading them, and apply the same scrutiny to agent-to-agent messages across untrusted boundaries.

### Never execute LLM-generated (or externally-sourced) commands with shell expansion
**Learning:** Commands built from LLM output or user input must run array-form (`shell=False`), never `shell=True` / string interpolation, and should be structurally validated first.
**Why:** `shell=True` passes the whole string to the OS shell; an argument with `;`, `&&`, or backticks becomes arbitrary code execution at the process's full privilege.
**How to apply:**
```python
# UNSAFE
subprocess.run(f"ffmpeg -i {inp} {out}", shell=True)
# SAFE
subprocess.run(["ffmpeg", "-i", str(inp), str(out)], shell=False)
```
Add allowlists (a `frozenset`) for any argument slot that must be constrained (codecs, formats). Auto-registering generated tools on `PATH` without signature verification is an additional supply-chain risk — avoid it.

### Security-gate test fixtures must mirror the production wire format
**Learning:** Fixtures for security-sensitive tests (PII scrubbers, validators, redactors) must replicate the data exactly as it arrives at the point under test in production — not the shape that happens to pass the current implementation.
**Why:** If a normalization step transforms the data before the gate (e.g. stripping hyphens from an ID), a fixture in the un-normalized form yields a false green: the test passes while real production data bypasses the check.
**How to apply:** Trace the data path from origin through every transformation to the point under test, and use that exact form. For redactors, cover *all* fields where the sensitive value can appear, and add a serialization-level assertion that the sensitive literal appears nowhere in the output — not just field-level checks.
