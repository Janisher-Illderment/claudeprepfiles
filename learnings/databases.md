# Databases (Supabase / PostgreSQL)

### Use `supabase.auth.getUser()` server-side, never `getSession()`
**Learning:** `getSession()` reads from local storage without re-verifying the JWT; `getUser()` makes a network call that validates the token and is the only safe basis for server-side authorization.
**Why:** A stale or tampered local session passes `getSession()`; only `getUser()` gives a server-verified identity.
**How to apply:** Use `getUser()` in any server handler/middleware making authz decisions; reserve `getSession()` for read-only client UI state (e.g. showing a username).

### Declare explicit GRANTs for every role that runs pgtap tests
**Learning:** pgtap simulates users via `SET LOCAL role = authenticated`; without an explicit `GRANT`, the role hits `permission denied` (fatal) *before* RLS even evaluates, so tests expecting "0 rows after RLS" become hard failures.
**Why:** App code works without grants because PostgREST uses the service role and applies JWT claims for RLS; pgtap sets the role directly with no such intermediary, so baseline table privileges must exist.
**How to apply:** Add a grants migration early:
```sql
GRANT USAGE ON SCHEMA public TO authenticated, anon;
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE <domain_tables> TO authenticated;
```
Test intentionally locked-down tables with `throws_ok` asserting `42501`, not `lives_ok` expecting empty results. Also: `CURRENT_DATE - INTERVAL 'N days'` returns `timestamp` in pgtap — cast `(... )::date` when a function expects `DATE`.

### Only set `objects_path` in Supabase storage config when real fixture files exist
**Learning:** `[storage.buckets.X] objects_path` makes `supabase db reset` upload every file in that directory to the bucket; if the directory is missing or holds placeholders like `.gitkeep`, reset fails with a path error or HTTP 400 `invalid_mime_type`.
**Why:** The feature pre-seeds dev blobs; it's harmful when the directory is empty or contains version-control placeholders that don't match the bucket's `allowed_mime_types`.
**How to apply:** Omit `objects_path` for buckets that start empty; add it only with real fixtures of the correct MIME types; never commit `.gitkeep` into such a directory.

### Index every column used in WHERE, ORDER BY, or JOIN
**Learning:** Unindexed foreign keys and filter columns cause sequential scans that degrade linearly with table size — a top, preventable source of query regressions.
**Why:** The planner can only use indexes that exist; ORMs create the FK constraint but usually not the matching index.
**How to apply:** After any migration adding a FK, or any query filtering/sorting/joining a new column, add the index:
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_events_created_at ON events(created_at DESC);
```
