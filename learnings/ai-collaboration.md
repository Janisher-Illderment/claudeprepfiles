# Working with AI Agents

Process, orchestration, and quality practices for collaborating with AI coding
agents (and for agents collaborating with each other).

## Process & accuracy

### Fetch official docs before running install commands
**Learning:** Never guess or infer install commands. Retrieve the official installation docs first and show the resolved command before executing.
**Why:** Incorrect install commands waste whole sessions and erode trust; the official path is often meaningfully different from what pattern-matching suggests (a dedicated installer vs. a package-manager flag).
**How to apply:** For any install (npm, pip, brew, winget, cargo…), web-search or fetch the official docs first, show the command, then run it. No exceptions, even for "obvious" tools.

### Never fabricate file paths, asset IDs, or external identifiers
**Learning:** When a path, asset ID, or external identifier isn't known with certainty, mark it `[UNCERTAIN]` and verify — don't pattern-match a plausible value.
**Why:** Fabricated paths produce code that looks correct but fails silently at runtime, and it broadly breaks trust in the assistant's output.
**How to apply:** Use glob/grep/file tools to confirm any path or identifier before embedding it. If verification isn't possible in context, flag `[UNCERTAIN]` explicitly.

### Apply explicit confidence markers
**Learning:** Use `[HIGH]` / `[MED]` / `[LOW]` / `[UNCERTAIN]` / `[ERROR]` markers so the assistant's confidence is auditable and false certainty doesn't reach production.
**Why:** Without self-labeling, outputs at very different confidence levels look identical; acting on a `[LOW]` claim unknowingly hides risk.
**How to apply:** Require markers on API-version assertions, file-path references, external-data claims, and any code generated without verified docs. Treat `[UNCERTAIN]` as a mandatory verify-before-merge step.

### Fail clearly instead of falling back silently
**Learning:** When a request can't be fulfilled correctly, return a descriptive error explaining what's missing and the alternatives — don't silently produce a qualitatively different result.
**Why:** A silent fallback looks plausible but violates the stated requirement; the user discovers it later, often after acting on it.
**How to apply:** Before adding a fallback, ask "would the user consider this result *incorrect*?" If yes, fail explicitly. Reserve silent fallbacks for strict quality degradation of the *same* intent (fewer results), never a different thing.

### Proactively include "how to test" after every implementation
**Learning:** End every implementation or install with a short "To test:" block containing the exact first command — don't wait to be asked.
**Why:** Making the user ask "how do I run this?" adds a round-trip and signals an incomplete handoff.
**How to apply:** Close with ≤5 lines: the first command to run plus required setup (venv activation, env vars). Applies equally to CLI installs and code changes.

### Clarify scope before refinement tasks
**Learning:** When asked to "refine / simplify / make more X" something that exists, ask one scoping question first: is the change about (1) content, (2) tone/wrapper, or (3) structure/order?
**Why:** "Simpler" is ambiguous — it usually means a tone change, not information reduction. Guessing wrong wastes a full iteration.
**How to apply:** Present the three options as a single question before drafting. Proceed only after scope is confirmed.

### Commit and surface state before context runs out
**Learning:** Near context limits, finish the current atomic unit, commit/push, and tell the user — don't create temporary session-log files.
**Why:** Temp logs add noise without recovery value; modern agent tools resume from git state directly.
**How to apply:** Around ~90% context used: complete the current task, `git commit && git push`, then signal the user to continue fresh.

## Multi-agent orchestration

### Enforce a fixed pipeline order
**Learning:** Follow research/retrieval → architecture (if needed) → implementation → code-review → commit. Skipping a stage — especially retrieval before external-API work — causes silent bugs.
**Why:** An implementation agent can't know what it wasn't given; skipping a research step before an API integration silently produced wrong field names and data loss.
**How to apply:** Before any external-API integration, run a retrieval agent for docs/schemas/rate-limits. Before non-trivial code, run an implementation agent on an adequate reasoning model. Before committing, run a review agent. Treat the pipeline as non-optional regardless of task size.

### Retrieve canonical sources before generating static data files
**Learning:** Before delegating creation of any reference data file (lists, pools, tables, catalogs), retrieve the authoritative source first — including a record count to validate against.
**Why:** Without a canonical source, an implementation agent constructs the list heuristically; it looks plausible but is wrong, and the error is invisible until a downstream test catches it.
**How to apply:** The brief must include the canonical source, a data-format sample, and the expected count, plus a validation assertion ("fail if count differs by >±10% from N").

### Keep implementation invocations atomic — one feature per invocation
**Learning:** Scope each implementation-agent invocation to a single atomic feature block; don't batch unrelated features.
**Why:** Mega-invocations burn tokens before a human can review, prevent course-correction, and make it impossible to attribute a bug to the change that introduced it.
**How to apply:** Default: one feature = one invocation = one commit. Group 2–3 only if tightly coupled and <~2h combined. After each, report what was created, committed, and what needs configuration.

### Separate implementation agents from review agents
**Learning:** The agent that implements a feature must never be the one that verifies it; always include a distinct review step before "done."
**Why:** Self-verification is a known failure mode — the implementer optimizes for completion and rationalizes its own choices.
**How to apply:** Run a review agent after every implementation, before merge/ship. "Low risk" (data-only, config) is not an exemption.

### Human instruction overrides automated hooks
**Learning:** When an automated goal/stop hook conflicts with an explicit human pause, surface the override option in the first response — don't force progress to satisfy the hook.
**Why:** Forcing hook satisfaction against an explicit pause wastes invocations and produces work the human will reject.
**How to apply:** On conflict, immediately offer: (1) clear/defer the hook, or (2) the subset of the goal not blocked by the pause. Never fire invocations solely to satisfy a hook the human paused.

### Route tasks to the appropriate model tier
**Learning:** Match model capability to task complexity: lightweight for simple/repetitive, mid-tier for standard professional work, high-capability only for novel/high-stakes/deep-reasoning tasks.
**Why:** A top model on formatting wastes budget; a small model on architecture or production debugging lowers the ceiling and raises error risk.
**How to apply:** Use a default tiered budget (≈60% light / 35% mid / 5% high) and adjust to actual work. High tier: architecture, security review, production debugging, subtle multi-file bugs. Light tier: formatting, rename, boilerplate, lookups.

### Delegate mechanical I/O to cheaper/local models
**Learning:** Route mechanical I/O — bulk file reads for context, boilerplate generation, summarizing long sub-agent output — to a lightweight or local model rather than a premium reasoning model.
**Why:** Premium tokens spent on deterministic output are wasted; static boilerplate needs no reasoning capacity.
**How to apply:** Delegate static HTML/MD/JSON, test scaffolds, changelog entries, module summaries, and >~2k-token sub-agent summaries. Do **not** delegate surgical edits, debugging, architecture, or security review. A 7–9B Q4 local model is ~GPT-3.5 class — it ignores format constraints on large/complex inputs, so prefer targeted grep/extract over piping whole large files to it.

### Understand worktree agent file behavior before merging
**Learning:** A worktree-isolated sub-agent may write files into the **main** working directory as untracked files rather than as commits on the worktree branch; the auto-merge then reports "already up to date."
**Why:** Worktree isolation doesn't guarantee the agent committed its output — that depends on whether the agent ran git itself.
**How to apply:** After any worktree-isolated agent, run `git status` in the main project dir; if output appears untracked/modified, stage and commit it manually. Don't rely on `git merge <worktree-branch>` to capture it.

## Skill, prompt & spec design

### Use a proposal-before-build guard for high-cost commands
**Learning:** For commands whose output is expensive to discard (large refactors, full redesigns, destructive migrations), have the model propose 2–3 directions and wait for confirmation before generating the final output.
**Why:** Users often realize they wanted something different only after seeing the first attempt; for expensive work, one wasted generation costs a lot or creates hard-to-revert diffs.
**How to apply:** Encode in the skill: "Before writing code: (1) propose 2–3 directions, (2) wait for selection, (3) only then implement." Apply to anything >~100 lines, shared-infra changes, or output needing line-by-line review.

### Require context-gathering before creative work
**Learning:** Make context-gathering non-optional: check loaded instructions → check a project config file → if neither, run a setup command before producing output.
**Why:** Without project context, AI defaults to statistically common patterns that may be entirely wrong for the specific product/audience/brand.
**How to apply:** Add a preamble to creative skills: "Before any work: (1) check loaded instructions for a `## Project Context` section, (2) check for `.project-context.md`, (3) if neither, stop and run `/setup-context`."

### Build multi-provider skill distributions with one transformer per provider
**Learning:** Keep one provider-agnostic Markdown source of truth and generate provider-specific output via a small (~50-line) transformer per provider; use placeholder substitution (`{{model}}`, `{{config_file}}`) for naming.
**Why:** Maintaining separate per-provider files causes drift; committing the generated `dist/` means end users need no build toolchain.
**How to apply:** `skills/` (edit here) → `scripts/transformers/{cursor,claude,gemini}.ts` → committed `dist/`. Emit a prefixed variant (e.g. `/i-audit` alongside `/audit`) to avoid namespace collisions across installed skill sets.

### Spec-driven development: write verifiable specs before implementing
**Learning:** For non-trivial features, produce a structured spec with explicit `WHEN/THEN` scenarios that can be validated before implementation.
**Why:** Discovering a missing/conflicting requirement at review time costs a full implementation cycle; a validatable spec catches it at design time.
**How to apply:** Use a spec-driven workflow (e.g. OpenSpec or equivalent): write `### Requirement:` blocks each with ≥1 `#### Scenario:` in WHEN/THEN, validate, then hand to implementation. Confirm existing capabilities first to avoid redundant specs.

### Design agents with single responsibility and explicit deliverables
**Learning:** Each agent should have one well-defined responsibility, specific deliverables, and quantifiable success metrics — not broad generalist scope.
**Why:** Generalist agents produce weaker output for lack of role-specific context and success criteria; specialization also makes handoffs explicit and auditable.
**How to apply:** Define: (1) identity/domain, (2) mission + scope boundary (what it does *not* do), (3) concrete deliverables with examples, (4) workflow, (5) quantifiable success criteria. Keep authoring and review agents strictly separate.
