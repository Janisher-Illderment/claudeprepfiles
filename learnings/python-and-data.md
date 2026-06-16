# Python & Data Pipelines

## Python language & packaging

### Wrap `ijson`-parsed data with a Decimal-safe JSON encoder
**Learning:** `ijson` returns every JSON number as `decimal.Decimal`; a later `json.dumps` then throws `TypeError: Object of type Decimal is not JSON serializable`.
**Why:** `decimal.Decimal` preserves precision but isn't natively JSON-serializable, so any path that re-serializes `ijson` output fails at runtime.
**How to apply:** Add the encoder proactively when `ijson` enters a module — don't wait for the error:
```python
import decimal, json
class _DecimalEncoder(json.JSONEncoder):
    def default(self, o):
        return float(o) if isinstance(o, decimal.Decimal) else super().default(o)
```
Route all `json.dumps` in the module through `json.dumps(obj, cls=_DecimalEncoder)`.

### Verify Python bitness before installing C-extension packages
**Learning:** A 32-bit Python on Windows fails to install modern C-extension packages (numpy, lxml, cryptography, greenlet…) because publishers stopped shipping 32-bit wheels; pip falls back to a source build that needs an MSVC toolchain.
**Why:** Without a matching wheel, pip compiles from source and fails on machines without the build toolchain.
**How to apply:** Check first: `python -c "import struct; print(struct.calcsize('P')*8, 'bit')"`. If 32-bit, skip pip and use `uv` (it embeds its own 64-bit Python): `uv tool install <cli>` for global CLIs, `uv pip install --python 3.12 <lib>` for venvs.

### Reconfigure stdout encoding in scripts that run as subprocesses
**Learning:** Python on Windows defaults stdout to the system ANSI codepage (e.g. `cp1252`) in non-interactive subprocess contexts; any out-of-codepage char (emoji, CJK) raises `UnicodeEncodeError` and kills the process.
**Why:** Python infers stdout encoding from the console locale at startup, and a subprocess doesn't inherit a UTF-8 console.
**How to apply:** At the top of any script meant to run as a subprocess:
```python
import sys
try:
    sys.stdout.reconfigure(encoding="utf-8", errors="replace")
    sys.stderr.reconfigure(encoding="utf-8", errors="replace")
except AttributeError:
    pass
```
Or set `PYTHONIOENCODING=utf-8` in the caller's environment as a stop-gap.

### Fix both the schema model and the serializer when a field is missing from an API response
**Learning:** A missing response field can be broken in two independent places — the output model (e.g. a Pydantic schema not declaring it) and the serializer (not populating it). Fixing one leaves the bug alive.
**Why:** Schema and serializer are separate concerns: a field in the serializer but not the model is stripped at validation; a field in the model but not the serializer stays `None`.
**How to apply:** Grep the field name in both the schema file and the serializer/router, and fix both in the same commit.

### Use frozen dataclasses with `__post_init__` validation for service config
**Learning:** A `@dataclass(frozen=True)` with validation in `__post_init__` gives immutable, fail-fast config that can't be mutated at runtime and catches misconfiguration at process start, not first use.
**Why:** Mutable config can be silently overwritten; deferred validation hides errors until production traffic hits them. Regex-validating external identifiers also blocks path traversal/injection.
**How to apply:**
```python
@dataclass(frozen=True)
class ServiceConfig:
    api_token: str
    allowed_targets: tuple[str, ...]          # tuple, not list — hashable on a frozen dataclass
    _SAFE = re.compile(r"^[A-Za-z0-9_]{2,64}$")
    def __post_init__(self):
        if not self.api_token: raise ValueError("token must be non-empty")
        for t in self.allowed_targets:
            if not self._SAFE.match(t): raise ValueError(f"invalid target {t!r}")
```

### Separate pure async services from framework-coupled layers for offline testing
**Learning:** Split business logic into a pure-async service layer (zero framework imports) and a thin framework adapter (cog/handler/controller); the service layer is then fully unit-testable with HTTP mocks, no framework boot required.
**Why:** Framework objects are hard to instantiate in tests; a service using only `aiohttp`/`httpx`/`asyncio` reaches 100% coverage offline with `aioresponses`/`respx`.
**How to apply:** `services/x.py` (pure) + `cogs/x.py` (only layer importing the framework). Create the HTTP session in `cog_load`, not `__init__` (keeps the constructor sync and testable); close it in `cog_unload`. Decorate background loops with an `.error` handler that logs but doesn't re-raise.

## Data pipelines & parsing

### Fetch the raw upstream payload before modifying a parser for external data
**Learning:** When a test fails processing data from an external service, the parser may already be correct — the upstream source may omit/reformat fields vs. its documentation. Patching the parser blind wastes time and can break working cases.
**Why:** Services don't always conform to their stated format in every codepath (e.g. an export tool drops an "always present" optional line).
**How to apply:** (1) Unit-test the parser against the documented format — if it passes, it's not the bug. (2) `curl`/`httpx` the live payload and diff field-by-field. (3) Then decide: handle the variant, or document the upstream limitation with a graceful fallback.

### Update hardcoded count assertions in the same commit as bulk data expansions
**Learning:** Adding entries to a static data file predictably breaks any test asserting an exact collection size; fix those assertions in the same commit, not reactively after CI.
**Why:** Count assertions guard "the full expected set is present" — valuable, but they become toil when treated as surprises.
**How to apply:** Before re-running tests after a data expansion, scan: `grep -n "assert len(\|== [0-9]\+" tests/`, update each count, and stage it with the data change.

### Never commit data entries with unverified values
**Learning:** Adding placeholder rows flagged `verified=false` / `[UNCERTAIN]` to shared data files creates a predictable add-then-revert churn and temporarily breaks count expectations.
**Why:** Uncertain data that turns out wrong has to be removed, inverting the work and breaking counts twice.
**How to apply:** Treat `[UNCERTAIN]`/`[MED]` data like missing data — don't commit it. Verify against an official source (or a second independent reference) first; commit only verified data, or get explicit written acceptance of the uncertainty.

### Build static datasets from one authoritative source — never fix reactively
**Learning:** Fixing individual wrong entries from assorted secondary sources causes drift and repeated rebuilds. Pick one canonical source, commit it as a fixture, and regenerate derived files from it.
**Why:** Secondary sources disagree, and "obvious" augmentations add entries the canonical source intentionally omits — each needing later removal.
**How to apply:** Commit the official dataset as `tests/fixtures/<name>_official.csv` (sole source of truth); regenerate `data/<name>.json` from it via a build script, never by hand. Entries absent from the CSV are absent from the project, full stop.

### Verify whether third-party export IDs are edition-specific or canonical before using them as foreign keys
**Learning:** Third-party catalog exports often use edition/printing-specific IDs while the authoritative reference DB exposes one canonical ID per logical entity; FK-ing export IDs to the canonical table fails for most rows.
**Why:** Catalog tools track physical/edition instances; reference DBs deduplicate to one record per logical entity — the two ID spaces are disjoint.
**How to apply:** Before creating the FK, confirm which ID space the import uses. If edition-level, store it in a non-FK column (or `NULL`) and join via a stable natural key (e.g. normalized-name lookup), not the numeric/UUID ID.

### Account for lossy integer arithmetic in converters; tolerate rounding downstream
**Learning:** A converter using integer division (`val // N`) is lossy in reverse; downstream code asserting the re-derived value equals the theoretical maximum gives false positives on real inputs.
**Why:** Integer division truncates — a value that rounds to 62 under `//8` can't round-trip to 66.
**How to apply:** Compute the max *lossless* value and assert against a tolerance (`>= MAX - tol`), not `== MAX`. Document the tolerance in an ADR so a future maintainer doesn't tighten it back.

### Use a build-script pattern for large datasets — generate, don't commit raw
**Learning:** Process large raw inputs (CSV, bulk exports) with a versioned build script that emits the smaller derived artifact; commit the script and its output, gitignore the raw input.
**Why:** Raw datasets are too large to track well, change independently, and contain more than the project needs; the script documents exactly what filtering/transformation happened.
**How to apply:** read → filter → enrich → write; gitignore raw inputs; print a distribution summary (counts by category) at the end so you can sanity-check before committing.

### Infer metadata from path structure with ordered, specific-first regex
**Learning:** When a dataset encodes metadata in its directory/file paths, regex against the path beats parsing file contents — but rules must run most-specific first with a documented fallback.
**Why:** Path hierarchies are intentionally structured by the publisher; path matching is cheaper and more consistent than content parsing.
**How to apply:** Order `(pattern, label)` rules specific→general, return on first match, default to `"unknown"`, and test against a dataset sample before the full run.

## Binary & external ingestion

### Reverse-engineering binary formats: document every offset and track dead ends
**Learning:** Binary-format work spawns many wrong hypotheses; record what was ruled out alongside what's confirmed, and remember layouts can be version-conditional.
**Why:** Without explicit dead-end tracking, the same wrong hypothesis gets re-investigated after every context loss; an offset correct for one version is wrong for another.
**How to apply:** Keep an offset map (offset, field, type, confirmed/speculative, version). Mark disproven ideas "dead end — reason", don't delete them. Change one variable at a time, verify against a known-good reference, and implement `BitReader`/`BitWriter` classes rather than inlining bit arithmetic.

### Use a purpose-built API library for JS-rendered content, not plain HTTP fetches
**Learning:** A plain GET on a JS-rendered page returns only the server shell (title, metadata), not dynamically rendered content (transcripts, comments); you need a library targeting the underlying data endpoint.
**Why:** JS-rendered content is assembled client-side after the initial response; a fetch that doesn't execute JS never sees it.
**How to apply:** E.g. for video transcripts use a dedicated transcript API library rather than scraping the page. Note that auto-generated captions contain phonetic errors — verify domain terms before encoding them into fixtures/docs.

## LLM-assisted CLI generation

### Separate build-time LLM generation from runtime invocation
**Learning:** LLM-driven CLI generation is most reliable when the LLM runs only at *build time* to emit reviewable, testable code; at runtime, callers invoke deterministic, versioned subcommands with no LLM in the path.
**Why:** Build-time generation produces a reviewable artifact; runtime NL→command translation adds latency, nondeterminism, and attack surface.
**How to apply:** Pipeline as Analyze → Design → Implement → Test → Document → Publish; ship a normal pip-installable CLI. Combine with array-form subprocess calls and argument allowlists (see [security.md](security.md)).
