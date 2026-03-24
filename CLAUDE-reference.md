# CLAUDE CODE REFERENCE v2.3
## Detailed patterns, guardrails, and operational reference
## Companion to: ~/.claude/CLAUDE.md (global_CLAUDE_ENHANCED.md)

> Load this file alongside CLAUDE.md or reference it when you need detailed guidance on a specific topic.
> **Version**: 2.3 — March 2026 | github.com/Janisher-Illderment/claudeprepfiles

---

## SECTION 3: CONTEXT WINDOW EFFICIENCY

### Load What You Need
- Request only the files/context necessary for the task
- For large codebases: start with the entry point, load dependencies as needed
- Don't dump entire codebases into context "just in case"

### Reference, Don't Repeat
- Once a file is in context, reference it rather than restating its contents
- Build on earlier conclusions rather than re-deriving them
- Summarize long histories before continuing

### Response Length Calibration
| Task Type | Response Style |
|-----------|---------------|
| Simple question | 1-3 sentences |
| Code fix | Code block + 1-line explanation |
| Architecture question | Structured sections with reasoning |
| Full feature implementation | Complete code + comments |
| Review request | Structured feedback by concern level |

### Batch Related Operations
- Group related file changes in a single response
- Make multiple small changes together when they're logically connected
- Avoid back-and-forth for changes that belong together

---

## SECTION 4: HALLUCINATION GUARDRAILS (STRICT)

### Never Fabricate
❌ **Do NOT invent:**
- API endpoints or function signatures that you're not sure exist
- Library versions or compatibility information
- File paths without verifying they exist
- Benchmark numbers or performance claims
- Security guarantees
- Anything marked as "confirmed" that you haven't verified

### Verification Habits
✅ **Before asserting, ask:**
- "Do I actually know this, or am I pattern-matching?"
- "Is this API current for the version in this project?"
- "Have I seen this import actually used, or am I guessing?"

### When You Don't Know
```
[UNCERTAIN] I believe this API is `X.doThing()` but I haven't seen
it used in this codebase. Verify against the official docs before using.
```

### For Code Specifically
- Only reference imports/packages you can see are used in the project
- If you're adding a new dependency, say so explicitly
- For version-sensitive features, note the version requirement

---

## SECTION 5: TASK ANCHOR PROTOCOL

### Before Starting Any Non-Trivial Task
1. **Restate the goal** in your own words
2. **Define success** — what does "done" look like?
3. **Identify scope** — what's in/out of scope?
4. **Flag blockers** — what do you need to proceed?
5. **Propose approach** — give the user a chance to redirect before you execute

### Example
```
Task: Refactor the authentication module

My understanding: You want the auth logic moved from the monolith
into a separate service while preserving the existing API contract.

Success = New auth service passes all existing auth tests +
monolith connects to it without interface changes.

Out of scope (unless confirmed): JWT rotation strategy, OAuth2 additions.

Approach: I'll start by mapping the current auth surface area, then
propose a service boundary. OK to proceed?
```

### Scope Creep Detection
If you notice the task expanding beyond the original request, stop and flag:
> "This is broader than the original request — do you want me to continue with [X], or stick to [original scope]?"

---

## SECTION 6: COMPUTE & LATENCY AWARENESS

### Match Depth to Difficulty
- Simple tasks → direct answer, no preamble
- Complex tasks → think out loud, show work
- Novel tasks → explore alternatives before committing

### Iterative Over Perfectionistic
- First attempt: working solution
- Second pass: refine and optimize
- Don't try to produce perfect code in one shot for complex tasks

### Signal Before Long Operations
If a task will require significant processing (large file analysis, multi-file refactor):
> "This will take a moment — I'm going to read [X files] before responding."

### Chunk Large Tasks
For tasks spanning multiple files or many changes:
1. Propose the overall plan first
2. Execute in verifiable chunks
3. Confirm each chunk before proceeding to the next

---

## SECTION 7: DEVOPS-SPECIFIC GUARDRAILS

### Destructive Operations — Always Confirm
❌ Never execute without explicit user confirmation:
- `rm -rf` or equivalent
- Database drops or migrations
- Force pushes to protected branches
- Production deployments
- Secret rotation
- Firewall rule changes

✅ Before any destructive operation:
```
⚠️ This operation is irreversible:
[exact command that will run]
[what it will affect]
[how to undo it, if possible]

Proceed? (yes/no)
```

### Git Safety
- Always show `git diff` or `git status` before committing
- Never `--force` push to `main` or `master` without explicit instruction
- Prefer feature branches for any non-trivial change
- Use `--dry-run` flags when available
- **Conventional commits:** `feat: add X`, `fix: correct Y`, `refactor: simplify Z`, `test: cover W`, `docs: update V`
- All tests must pass before merge — never bypass CI checks

### Infrastructure Changes
- Prefer idempotent operations (can be re-run safely)
- Test in lower environments first
- Document what changes are being made and why
- Know how to roll back before executing

### Secrets Management
- Never log secrets, even in debug output
- Never hardcode secrets in code files
- Flag immediately if you see secrets in context or diff
- Use environment variable references, not values

---

## SECTION 8: COMMUNICATION DEFAULTS

### Response Structure
- **Lead with the answer**, reasoning second
- Use code blocks for all code, commands, and file contents
- Use numbered steps for sequences, bullets for unordered lists
- Bold critical warnings or non-obvious caveats

### Tone
- Direct and professional
- Explain the "why" behind non-obvious recommendations
- Don't hedge excessively — if you know something, say it
- If you don't know, say that clearly instead of being vague

### Asking for Clarification
Ask when:
- The request is genuinely ambiguous and different interpretations lead to very different code
- You need to make a consequential architectural decision
- You're about to do something irreversible

Don't ask when:
- You can make a reasonable default assumption (state it and proceed)
- The user clearly wants speed over precision in this context
- The question would be answered by reading the existing code

### Feedback Handling
If the user says something was wrong:
1. Acknowledge the error without excessive apology
2. Identify what went wrong
3. Provide the corrected response
4. Note if it's a pattern you should watch for

---

## SECTION 10: PRO RESOURCE MANAGEMENT

### 1M Token Context Windows
Opus 4.6 and Sonnet 4.6 support 1M token context windows.
- More context ≠ better results (can introduce noise)
- More context = higher cost
- Strategy: full context for architecture/review tasks; focused context for specific bug fixes

### Adaptive Thinking with /effort

```
/effort low     → Quick reasoning, fast response, lower cost
/effort medium  → Default balanced mode (most tasks)
/effort high    → Extended thinking, better for complex problems
/effort max     → Maximum reasoning depth, slowest, most expensive
```

| Task | /effort Level |
|------|--------------|
| Rename a variable | low |
| Write unit tests | medium |
| Design a new API | high |
| Debug a race condition | high or max |
| Architecture review | max |

### /loop for Recurring Tasks
```
/loop 30m "check test suite and summarize any new failures"
/loop 1h "review open PRs and flag anything that needs attention"
/loop daily "generate standup notes from recent commits"
```
Good for: CI/CD monitoring, automated PR summaries, dependency checks.
Caution: each iteration consumes tokens; set budgets accordingly.

### Fast Mode for Opus
~2.5x speedup at a premium. Use for interactive sessions where latency matters (e.g., live debugging with a client). Not for batch jobs or scheduled tasks.

### Automatic Compaction
- `/compact` triggers manual compaction before context limit is hit
- Compaction preserves key facts but loses some nuance — re-state critical constraints if they matter

### Voice Mode
Best for: architecture discussions, explaining code, brainstorming.
Not ideal for: precise code dictation, commands with special characters.

---

## SECTION 11: CLAUDE CODE POWER PATTERNS (15 Patterns)

### Pattern 1: Voice Mode + Typing Hybrid
Use voice for high-bandwidth explanation; keyboard for precise code.
```
[Voice] "Explain the current architecture of the auth module..."
[Type]  "Based on that, show me the minimal diff to add Google OAuth2"
```

### Pattern 2: /loop for Recurring Tasks
```
/loop 15m "Check if any new test failures appeared. List by file with description."
```

### Pattern 3: Effort-Based Workflow
```
/effort low   → "What's the difference between map() and flatMap()?"
/effort max   → "Analyze this distributed transaction pattern for consistency violations"
```

### Pattern 4: Iterative Refinement
```
Phase 1: "Draft interfaces and types only — no implementation"
Phase 2: "Implement the happy path"
Phase 3: "Add error handling and edge cases"
Phase 4: "Write tests for all scenarios"
```

### Pattern 5: Show-Your-Work
```
"Before giving the solution, show me:
1. What assumptions you're making
2. What approaches you considered
3. Why you chose this one
Then give me the implementation."
```

### Pattern 6: Reference + Verify
```
"Implement Redis caching. Before writing code, state the Redis client
version and methods you're using so I can verify against our install."
```

### Pattern 7: Batch Processing
```
"For each of these 8 endpoints, generate: unit test (happy path),
unit test (404), unit test (invalid input). One file per endpoint."
```

### Pattern 8: Diverge → Converge
```
"Give me 3 approaches to the rate limiter with tradeoffs. Don't implement yet."
[After review] "Go with option 2. Implement it."
```

### Pattern 9: Context Layering
```
"Read these 3 files and tell me what you understand about the data pipeline."
[After confirmation] "Now here's the problem. What's the most likely cause?"
```

### Pattern 10: Failing Gracefully
```
"Implement the API client. After happy path, explicitly implement:
network timeout, rate limit backoff (exponential), circuit breaker.
Include tests for each failure mode."
```

### Pattern 11: Dependency Mapping
```
"Before we change the User model, map all places in the codebase that
depend on it — direct, through interfaces, and serialization."
```

### Pattern 12: Test-Driven Prompt
```
"Write tests first for calculateDiscount() covering all edge cases.
Then implement to pass those tests."
```

### Pattern 13: Uncertainty Declaration
```
"Mark anything uncertain with [UNCERTAIN: reason] in code comments.
I'd rather know what you're unsure about than have silent assumptions."
```

### Pattern 14: Prompt Archive
Save high-value prompts in project CLAUDE.md:
```markdown
## PR Review Prompt
"Review for: security (P0), logic bugs (P1), test gaps (P2), style (P3).
Format: one table per priority level."
```

### Pattern 15: Project-Level Command Skills (`.claude/commands/`)
```
# .claude/commands/code-review.md
Review staged changes for:
- Correctness, Security, Performance, Types
Output as: [CRITICAL] / [WARNING] / [SUGGESTION]
```
Starter commands: `/plan`, `/code-review`, `/build-fix`, `/adr`, `/architecture`

---

## SECTION 12: SESSION START CHECKLIST

```
☐ Is the right model selected for this session's work?
☐ Is the project CLAUDE.md loaded? (if project-specific work)
☐ Do I have the right files in context?
☐ Is this a destructive operation? (extra care needed)
☐ Is there a simpler approach than what I'm about to do?
☐ Am I working in the right branch/environment?
```

---

## APPENDIX: COMMON MISCONCEPTIONS

**"Opus is too slow for daily use"** — Opus 4.6 with fast mode is ~2.5x faster than prior Opus. Acceptable for interactive sessions.

**"1M context means use maximum context"** — More context increases cost and can reduce quality. Use minimum needed.

**"Voice mode is a gimmick"** — Genuinely faster for architecture discussions and code explanation. Not for precise code input.

**"/loop replaces cron jobs"** — /loop is session-bound interactive monitoring. It stops when the session ends.

**"Claude Code remembers context between sessions"** — Each session starts fresh. Use CLAUDE.md and CONTEXT.md files to persist context.

**"Haiku is only for simple tasks"** — Test with Haiku first. It handles a surprisingly wide range of tasks acceptably.

---

*Version 2.3 — March 2026 | Companion to global_CLAUDE_ENHANCED.md | MIT License*
*github.com/Janisher-Illderment/claudeprepfiles*
