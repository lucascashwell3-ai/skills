---
name: build-verification
description: How to actually prove a build works rather than just claim it — a 3-layer verification stack (fast mocked unit tests, real protocol/integration smoke test, live dogfood run with independent ground truth), plus habits for proving "non-destructive" claims and handling git hygiene before a commit. Use whenever finishing a feature, fixing a bug, or about to tell the user "this works" or "this is done."
metadata:
  type: reference
---

Distilled from building rls-guard (an MCP server). The user's standing instruction is "verify, don't assume" — this skill is the concrete version of that for code changes. Never report success you didn't witness; if you can't run a layer, say so explicitly instead of skipping it silently.

## The 3-layer stack

A claim of "it works" needs evidence from all three layers that apply to the thing being built. Skipping a layer because "the others passed" is how confident-but-wrong reports happen.

**Layer 1 — Fast unit tests, mocked I/O, no network.** Cover the full decision/severity matrix, not just the happy path — every branch that changes behavior, plus error paths (timeouts, malformed responses, auth failures). rls-guard's `test/audit.test.mjs` mocks `fetch` and asserts the entire severity matrix in one test (`profiles` → CRITICAL, `posts` → HIGH, `published_items` → INFO, `subscriptions` → LOW, `ghost` → OK) rather than one test per case. Target: runs in under a second, runnable on any machine with zero external dependencies. Run via `npm test` or equivalent before moving to layer 2.

**Layer 2 — Protocol/integration smoke test.** Boots the *real* interface and exercises it end-to-end, not a mock of it. For an MCP server this means actually spawning the built server over stdio, connecting a real client, listing tools, and confirming the live tool registration and input schema match what the code claims — see `scripts/smoke.mjs`. This layer catches the class of bug unit tests can't: wiring, registration, serialization, the build artifact not matching the source. Also include at least one deliberate negative-path call (bad input) and confirm it fails *cleanly* (a structured error) rather than crashing.

**Layer 3 — Live dogfood run against a real instance, checked against independent ground truth.** Point the tool at a real target and cross-check its findings against something else that already knows the right answer — another tool's report, a manual inspection, a known-seeded vulnerability. rls-guard's first credible proof wasn't the test suite passing — it was running it against prompt-library-manager's live Supabase project, finding a real anon-INSERT gap, and confirming that finding against the actual Supabase dashboard before calling it done. A test suite passing is necessary but not sufficient; a live run against ground truth is what actually proves the tool produces correct, trustworthy output in the real world.

## Proving "non-destructive" or "read-only" claims

Never assert "this is safe / read-only / non-destructive" without a concrete before/after measurement that proves it. The assertion itself is not evidence. For rls-guard's insert probe, the proof is: an empty-payload INSERT with PostgREST's `Prefer: tx=rollback`, then confirm row counts are identical before and after the probe — not just "should roll back," but counted before, counted after, diffed. If the claim is "this query doesn't change row count," run the count, run the operation, run the count again, and show the delta is zero. If you can't take a before/after measurement for a given claim, that claim isn't proven yet — say so rather than asserting it.

## Git hygiene before committing

- Scan the diff for secret patterns (API keys, private keys, tokens, `service_role`, connection strings with embedded credentials) before staging or committing. Don't rely on `.gitignore` alone — check the actual diff content.
- If you hit a stale lock file or other filesystem anomaly mid-task, investigate before deleting: `ps aux` / `lsof` to see what process (if any) holds it, check the file's age and contents. A lock file might represent another process's in-progress work, not garbage — treat it the way you'd treat any other unfamiliar state per the "investigate before deleting" rule.
