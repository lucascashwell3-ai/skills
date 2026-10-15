---
name: exposure-scanner-design
description: Design lessons for building any exposure/audit-style security scanner (RLS checkers, permission auditors, config scanners) — black-box probing beats trusting introspection/config when introspection is restricted, and severity must be weighted by data sensitivity, not mere exposure, to avoid false positives that erode trust. Use when designing or reviewing a tool that audits a system for security misconfiguration.
metadata:
  type: reference
---

Two lessons from building rls-guard, an MCP server that audits a Supabase project's `anon`-role exposure (read/write/RPC).

## Lesson 1 — black-box probing beats introspection when introspection is locked

The naive design is: ask the system for its config/schema, then reason about exposure from that. This breaks the moment the system locks introspection down — and modern Supabase does exactly this; the anon key can no longer pull the PostgREST root OpenAPI schema (that now requires `service_role`). rls-guard's fix: accept an explicit target list from the caller (the calling agent already knows the table names from migrations or generated types) and then **probe each one directly with the restricted credential** rather than trying to enumerate them.

This isn't just a workaround — it's often the *more correct* design even when introspection is available. Probing with the actual restricted credential answers "what can this credential really do" directly; trusting stated config answers "what is the config supposed to allow," which can drift from reality (a permissive policy, a misapplied grant, RLS toggled off after the schema was last read). When the choice is between "ask what should be true" and "test what is true," test what is true — see `src/audit.ts`'s read probe (live GET against PostgREST, reading row counts back) and insert probe (empty-payload INSERT with `tx=rollback`, reading the resulting error code to distinguish "blocked" `42501`/401/403 from "got past the check"). Apply this whenever you're auditing IAM policies, API permission scopes, file ACLs, or any other access-control surface: prefer "try the actual restricted action and observe the real response" over "read the policy and infer."

## Lesson 2 — severity must be weighted by sensitivity, not mere exposure

A scanner that flags "is this readable by an under-privileged role?" as uniformly dangerous will be *technically correct* and *practically useless*, because most systems have intentionally public surfaces. rls-guard's first pass flagged a table named `published_prompts` as a critical leak — it was readable by anon, full stop. But the table held intentionally public content with no sensitive columns; the "leak" was working as designed.

The fix was a third severity path beyond "exposed" and "not exposed": an **intentionally-public tier**. A table downgrades from HIGH/CRITICAL to INFO when its name matches a public-by-design pattern (`published_*`, `public_*`, `catalog_*`, and similar) *and* it has no sensitive columns (no PII, credentials, tokens, billing identifiers). Both conditions must hold — a public-looking name with an `email` column still escalates. See the severity matrix test in `test/audit.test.mjs`: `profiles` (sensitive name + sensitive column) → CRITICAL; `posts` (neutral, exposed) → HIGH; `published_items` (public-pattern name, nothing sensitive) → INFO; `subscriptions` (sensitive name, zero rows currently) → LOW/confirm rather than a hard alarm.

The general principle: any exposure scanner needs a notion of "expected/intended exposure" as a first-class severity input, not an afterthought bolted on after complaints. **False positives erode trust in a scanner faster than missed positives do** — a user who gets cried-wolf-at on intentionally public data starts ignoring the tool's real findings too. When designing severity logic, always ask "what does this system's owner almost certainly intend here?" before classifying something as urgent, and bias toward a graduated scale (a CONFIRM/INFO tier) over a binary alarm/no-alarm split.
