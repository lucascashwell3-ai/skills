---
name: boris
description: The Boris Cycle — a repeatable audit-and-cut process that keeps an AI workspace (instruction files like CLAUDE.md or AGENTS.md, docs, memory, skills, plugins, scheduled automation) lean without losing anything. Use when the setup feels bureaucratic, the AI ignores instructions, outputs won't stay short, docs contradict each other, or once a month and after any big reorganization. Works on any AI setup with files.
license: MIT
---

# The Boris Cycle

Named in honor of Boris Cherny, creator of Claude Code. The insight: **the model reads
everything you keep.** A stale doc isn't neutral clutter — it's a wrong fact injected into every
future session. Keeping it is the risk; pruning it is the safety measure.

**The one distinction that resolves "lean" vs "losing context":**
- **State** — what's true now. Tiny, one home per fact, always fresh, loaded every session.
- **History** — how it got that way. Archived, retrievable on demand, NEVER loaded by default.
A setup rots when history leaks into state surfaces.

## Safety contract (non-negotiable, announce it first)

1. **Archive, never delete.** Prune = MOVE to an archive folder with a README saying what, why,
   and where the living version is. Git history underneath = second net. Everything recoverable.
2. **Read before you kill.** Any surface being retired gets read first; live items in it
   (to-dos, open decisions, unshipped ideas) migrate to the surviving surface BEFORE the move.
3. **Don't touch running agents' ground.** If other sessions/agents are working, restrict writes
   to files they don't own; `git pull --rebase` before every push; merge conflicts by keeping
   BOTH sides' intent, never by discarding theirs.
4. **Secret-scan every diff before committing.**

## Phase 1 — Inventory (read-only, ~10 min)

Measure, don't vibe. Collect:
- **Docs:** every instruction/status/handoff file — line count, last-modified, last-commit date.
- **Memory:** file count, total words, per-file age.
- **Skills + plugins:** what's installed, and what each costs (every skill description loads into
  every session).
- **Automation:** every scheduled job/routine/hook — what it reads, what it WRITES.

## Phase 2 — Measure real usage

Data beats affection. People defend tools they never use.
- Skill invocations from transcripts:
  `grep -rhoE '"skill"\s*:\s*"[^"]+"' <transcripts-dir> --include='*.jsonl' | sort | uniq -c | sort -rn`
- Connectors/plugins never authenticated = provably unused.
- A dashboard/doc nobody opens = dead. Ask the owner one blunt question: "do you ever open this?"

## Phase 3 — Diagnose the four rots

1. **Duplication** — the same fact living in N files. They WILL drift; every session pays to read
   all N. Pick the one canonical home per fact-type; everything else becomes a pointer or dies.
2. **Staleness** — docs that lie (describe removed systems as running, list shipped work as
   pending). The most dangerous rot: a fresh session may act on it. Find by comparing
   last-commit dates against what actually happened since.
3. **Bloat** — skills/plugins/instructions with near-zero usage taxing every session's context.
   Instruction dilution is why "be terse" stops working: your rules compete with a hundred
   descriptions and process gates, and the pile wins.
4. **Automation drift** — scheduled jobs still writing surfaces you're retiring. Kill a surface
   without re-pointing its writers and it resurrects tonight.

## Phase 4 — Execute (in this order)

1. Migrate live items out of anything being retired (Safety rule 2).
2. Move retired files to `archive/<date>_<reason>/` + write the README.
3. Rewrite the surviving state surfaces short: a handoff file ≤ ~50 lines; status = facts only;
   done items leave entirely (git history is the record).
4. Update every constitution/instruction file that referenced the dead surfaces.
5. Re-point automation (routine prompts, hooks) at the surviving surfaces; explicitly forbid
   recreating the dead ones.
6. For each barely-used plugin: uninstall, and vendor the 1–2 skills actually used as personal
   skills (patch their cross-references to now-missing siblings).
7. Commit + push everything touched. Verify: fresh `git status` clean everywhere.

## Phase 5 — Install the ritual (so it stays lean)

One rule added to the standing instructions, enforced every session:
> Any doc this session proved stale gets a superseded banner or moves to archive — same session.
> Done items leave the handoff surface entirely.

Plus a recurring full cycle (monthly, or after any reorganization): rerun Phases 1–4. Memory
files get the same treatment on that cadence — merge overlapping entries, delete only what's
WRONG, archive what's merely old.

## Report format

Verdict first ("the problem is X, not Y"), then evidence per rot, then what moved where. Numbers
over adjectives: "18 invocations in 37 sessions, 12 = one skill" lands; "seems underused" doesn't.

## Portability note

Nothing here is specific to one setup. Map the nouns: constitution file(s) = CLAUDE.md /
system prompts; state surfaces = status/handoff docs; automation = cron, scheduled routines,
CI bots, hooks. The same cycle applies to a team's shared setup — and to any tool that INSTALLS
resources into someone's AI environment: integration must be surgical (fit the user's existing
state, one home per fact, no duplicate instruction mass), or the install itself is the clutter.
