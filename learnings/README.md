# Learnings — Field-Tested Engineering Gotchas & Best Practices

A curated, **person-agnostic** knowledge base of hard-won learnings, gotchas, and
how-tos distilled from real project work. Every entry follows the same shape:

> **Learning** — the lesson in 1–3 sentences ·
> **Why** — the root cause / rationale ·
> **How to apply** — concrete, actionable guidance (commands and snippets where useful)

These are not tutorials; they are the non-obvious things that cost time the first
time and shouldn't cost it again. Nothing here is tied to a specific person,
private project, or proprietary domain.

## Index

| File | Topics |
|------|--------|
| [ai-collaboration.md](ai-collaboration.md) | Working with AI agents, multi-agent orchestration, model routing, confidence markers, skill/prompt design, spec-driven development |
| [security.md](security.md) | Secret lifecycle, untrusted-input boundaries, subprocess safety, prompt injection, security-gate fixtures |
| [python-and-data.md](python-and-data.md) | Python packaging/encoding/serialization, data pipelines, parsing, build-from-source-of-truth, binary formats |
| [javascript-web.md](javascript-web.md) | Deno, TypeScript, React, pnpm, vanilla JS, CSS layout, Tailwind, LLM API integration |
| [databases.md](databases.md) | Supabase auth, pgtap/RLS, storage config, PostgreSQL indexing |
| [deployment-ci-windows.md](deployment-ci-windows.md) | PaaS deploy, Docker, CI/CD, GitHub Actions, Windows/PowerShell, Git CLI, tunnels, local-LLM ops |
| [documents-latex.md](documents-latex.md) | LaTeX class/float pitfalls, self-contained includes, PDF/FDF annotation extraction |

## Contributing an entry

Keep it generalizable: strip project names, personal identifiers, and absolute
user paths. If a lesson came from a niche domain, abstract the domain away and keep
only the transferable engineering principle. Use the Learning / Why / How-to-apply
shape and place it under the closest existing domain header.
