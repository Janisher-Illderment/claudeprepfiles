# Documents & LaTeX

Pitfalls from migrating and compiling real LaTeX projects (thesis-class templates,
generated reports) and from extracting reviewer comments out of PDFs.

### A `newfloat`-declared floating environment can hang compilation in an infinite pagination loop
**Learning:** A document class that declares a custom float via `\DeclareFloatingEnvironment` (the `newfloat` package) can enter an infinite pagination loop: the document typesets all pages but never writes "Output written", the `.aux` stays empty, refs stay undefined, and the run times out with no error.
**Why:** The float's placement/pagination interacts badly with the class; the loop is independent of `[H]`/`[ht]` placement, line numbering, or caption format — any *use* of the broken float triggers it, while the inner content environment alone (e.g. bare `algorithmic`) compiles fine.
**How to apply:** Bisect with a minimal document (compiles in seconds vs. minutes) to confirm the offending environment, then replace the float wrapper with a non-floating equivalent: a bold, manually-numbered header + the intact inner environment (e.g. `\par\medskip\noindent\textbf{Algorithm 1: …}` followed by `\begin{algorithmic}…`). Don't try to load a conflicting package (`algpseudocode` when the class already defines `\algorithmic` → "already defined").

### Keep shared `\input` includes self-contained — don't depend on a counter defined only in one master
**Learning:** If an included file (chapter, appendix) uses `\refstepcounter{foo}`/`\thefoo` where `\newcounter{foo}` lives only in one master document's preamble, compiling that include from any other master leaves the counter undefined and errors out.
**Why:** Includes are often reused across multiple master documents; preamble-defined state doesn't travel with the include.
**How to apply:** Either define such counters inside the include itself, or hardcode the values when the items aren't cross-referenced. Prefer self-contained includes that compile under any master.

### Custom TikZ style names must not collide with reserved keys
**Learning:** Naming a TikZ style after a reserved key — notably `out` (the curve out-angle key) — yields `Package pgfkeys Error: The key '/tikz/out' requires a value`.
**Why:** `[out]` is parsed as the reserved curve key, not your style.
**How to apply:** Name styles to avoid reserved words (`outf`, `outbox`, `result`…). Reserved/`to`-path keys to dodge include `in`, `out`, `at`, `to`.

### Run pdfLaTeX passes separately when warnings make it return non-zero
**Learning:** Chaining passes with `pdflatex a && pdflatex a && pdflatex a` can stop early because pdfLaTeX returns a non-zero exit on warnings, not just errors.
**Why:** `&&` aborts the chain on the first non-zero exit, which warnings can produce.
**How to apply:** Run the passes as separate commands (or with `;`), and gate "success" on the log (`Output written on …`, `undefined: 0`, `multiply: 0`) rather than the exit code. Three passes resolves TOC/figure/table indices and cross-references.

### Don't let multiple `pdflatex` processes compete for the same `.aux`
**Learning:** Several concurrent `pdflatex` runs on the same document block each other fighting over the `.aux`, producing hangs and corrupt auxiliary state.
**Why:** The `.aux`/`.toc` files are single shared artifacts; concurrent writers corrupt them.
**How to apply:** Ensure only one compile runs at a time; kill stragglers first (`Get-Process pdflatex | Stop-Process -Force` on Windows) before re-running.

### Extract PDF reviewer comments from an FDF without PDF libraries
**Learning:** Annotation/comment text exported as an `.fdf` is binary-ish but parseable directly: each comment is a `/Contents (...)` PDF string (count them to verify total), often UTF-16BE with a `FEFF` BOM, with a nearby `/Page N`.
**Why:** When no PDF library is installed, you can still recover all reviewer comments by parsing the FDF's PDF-string syntax (balanced parens, `\`-escapes and octal, UTF-16BE decode).
**How to apply:** Regex-count `/Contents` to confirm the comment count, then for each: read the balanced `(...)` (or `<…>` hex) string, decode UTF-16BE if it starts with `\xfe\xff` else latin-1, and pull the adjacent `/Page (\d+)` for location. Sort by page to reconstruct the review in document order.
