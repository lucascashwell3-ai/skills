# How to review — the four lenses, the finding block, the stopping rules

Read this and `sources.md` before judging anything. Then apply the lenses **in order**. The order is
the point: a report full of naming nits that missed a broken claim or a wrong data structure is a
report that wasted everyone's time.

---

## Lens 1 — Data structures first

`sources.md` § 1, § 2.

Before any function-level finding, ask what shape the data is in and whether a different shape
deletes work.

- Which branches exist only because of how the data is arranged? A special case for "empty", "the
  first one", "the last one", "the logged-out one" is the classic tell.
- Is the same fact stored in two places, so the two can disagree? That is a bug waiting for a date.
- Is state derived where it could be computed, or computed where it must be stored?
- Are there types that permit states the product can never legitimately be in?

**A finding that removes a branch outranks any finding that corrects one.** If you find one, it goes
first in the report, whatever its severity would otherwise be.

## Lens 2 — Does it break anyone downstream?

`sources.md` § 3.

Map "userspace" onto whatever depends on this code. Then look for changes that break it:

- A rename, moved route, or changed URL that someone has bookmarked or linked.
- A schema or migration that drops or reshapes rows that already exist. Read migrations especially
  carefully — they are the one thing here that cannot be reverted by a git revert.
- A changed response shape, error code, or default that another build reads.
- Auth or row-level-security rules loosened, even briefly. On a repo where the database rules *are*
  the security boundary, this is always the top finding.
- Anything that changes behavior people already paid for or relied on.

Rank these above correctness. A bug affects the next user; a break affects every existing one.

## Lens 3 — Did the build do what it claimed?

**This is why the skill exists.** Everything else is available elsewhere; this is not.

Take the claim list gathered in Step 1 — the session's own summary, the pull request body, the
commit messages, the status file — and check each claim against the code, the tests, and a run.

For every claim, one of four verdicts:

- **TRUE** — the code does it and something proves it. Name what proved it.
- **PARTIAL** — the happy path does it, something adjacent does not. Say exactly which part.
- **UNPROVEN** — plausible, but nothing here demonstrates it and you could not run it. Not an
  accusation. Say what would settle it.
- **FALSE** — the code does not do this. **Always the top finding in the report**, above every
  design and correctness finding, no exceptions.

Where to look for the gap between claim and reality:

- A test that asserts the mock, not the behavior. A test that would pass with the feature deleted.
- A feature that exists but is never called, imported, routed to, or rendered.
- Error handling written as an empty `catch`, so the failure is claimed to be handled and is
  actually swallowed.
- "Tested" or "verified" with no command output anywhere in the record.
- A config, environment variable, or migration the code needs and nobody added.
- A claimed fix whose original failure was never reproduced, so nobody knows it was the cause.

**Run the verification command yourself if you can.** A claim checked by reading is worth less than
a claim checked by running, and the difference goes in `CONFIDENCE`.

## Lens 4 — Will the next person survive this?

`sources.md` § 4, § 5, § 6, § 7, § 8.

Last, and only after the first three:

- Functions doing several things; nesting deep enough to signal the design is wrong (§ 4).
- Commits carrying unrelated changes, so neither can be reverted alone (§ 5).
- Work finished and never landed — unpushed commits, unmerged branches, a pull request nobody
  reviewed. Producing faster than you accept is not speed (§ 5).
- Commit messages and pull request bodies that say what changed and never what was wrong (§ 6).
- Comments explaining a confusing block instead of the block being fixed (§ 7).
- Error handling duplicated across many exit points, or swallowed silently (§ 8).
- Dead code, dead flags, dead config, and dependencies nothing imports.

---

## The finding block

Every finding, this exact shape. No prose reports. IDs are assigned by the agent that found it, with
its own prefix (`R1-03`), and never change afterward.

```
### <PREFIX>-<NN> — <title, max 60 chars>
TIER:       blocking | worth-fixing | polish
LENS:       data | breaks-users | claim-check | maintainability
CONFIDENCE: observed | inferred
SAW:        <file:line, or the command and its output. Quote the code. Never an adjective.>
COSTS:      <the consequence, concretely. Not a style opinion.>
FIX:        <the smaller design that removes the cause. Never a complaint without one.>
EFFORT:     trivial | small | medium | large
SOURCE:     sources.md § <n> — <quote fragment>   |   reviewer's own read
```

Tier is what it costs, not how ugly it looks:

- **blocking** — a false claim, a break for someone downstream, or data loss. Ships over this and
  something real goes wrong.
- **worth-fixing** — a real defect or a design that will cost repeatedly.
- **polish** — true, small, and safe to ignore this week.

Close with **"The three I'd fix first, and why."**

---

## Stopping — put these in every reviewer's prompt, verbatim

A reviewer that keeps re-checking instead of writing it down burns tokens and returns nothing. That
is a real failure mode, not a hypothetical.

- **Confirm once, then move on.** A finding you have observed is done. Re-running a check to be
  extra sure is forbidden — note the doubt in `CONFIDENCE` and continue.
- **Hard budget: ~40 tool calls.** At 40, stop investigating and start writing, wherever you are.
- **Three strikes on any single check.** If a command, build, or test fails three times, abandon it,
  record it as unverifiable, and move on. Never a fourth attempt.
- **Partial beats perfect.** An incomplete report delivered is worth more than a complete one that
  never arrives. Findings not yet written up are findings that do not exist.
- **Never re-verify another reviewer's ground.** Reviewers run in parallel; overlap is waste.
- **Do not pad.** Twelve sharp findings beat forty soft ones. A review concluding "this is great"
  has failed; so has one that manufactures complaints to look thorough.
