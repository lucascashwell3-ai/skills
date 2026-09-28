# The report, the plan, and fixing

## Report format

Written for the owner, not for another agent. Plain words. No reviewer jargon — a report nobody can
read is a report nobody acts on, and that is the most common way a review of this kind fails.

```markdown
# Linus review — <what was reviewed>, <date>

**Verdict.** <One sentence naming the real problem. A claim, not a summary.>

<Two or three sentences of argument, strongest case first. If the build's own description of itself
is wrong, that goes here, in the first line, before anything else.>

**The claims, checked.**
| what was claimed | verdict | what proved it |
|---|---|---|
| <claim, in the words it was claimed> | TRUE / PARTIAL / UNPROVEN / FALSE | <command output, file:line, or "nothing"> |

**Measured.** <n> files reviewed · <n> findings (<n> blocking) · verification: <the command, and
whether it passed> · <anything you could not check, named as unverified>.

---

## The findings, ranked

### 1. <Imperative title> — blocking · <trivial / small / medium / large>
**What's wrong.** <One or two sentences.>
**Evidence.** <file:line, or command and output. Quote the code. Never an adjective.>
**Costs you.** <The consequence, concretely.>
**Fix.** <The smaller design that removes the cause.>
**Source.** sources.md § <n> — <quote fragment> · or: reviewer's own read.
<[CONVERGED] — found independently by <which reviewers>. Only at project scope.>

### 2. …

---

## What I'd leave alone
<Two to four things that look wrong and are actually fine, and why. A review that only attacks is a
review that wasn't read carefully — and this section is what makes the attacks credible.>

## What I couldn't check
<Every claim needing a file, command, or environment that wasn't reachable. Say what would settle
each one.>

---

## The plan
<The findings as work, smallest first, each one its own commit. Say what each commit changes and
what proves it worked. Stop at ten; if there are more, say how many were cut and on what rule.>

1. <commit-sized change> — proves it: <the command or check>
2. …

## Your calls
1. <short title>                          do / skip / later
2. <short title>                          do / skip / later
```

Rules for the writing:

- Verdict in the first line. No preamble, no restating the request, no narrating the process.
- A **FALSE** claim goes above everything else in the report, always, whatever its tier would be.
- Numbers and quoted code, not adjectives. "Feels fragile" is not a finding.
- Never a complaint without the fix beside it.
- **Cap the ranked prose section at ten**, and never truncate silently — say how many were cut and
  on what rule. The cap is about how much prose a person will read, nothing else.
- Never soften a finding with praise. Good parts go in *What I'd leave alone*, once.
- Convergence outranks any single reviewer's tier. Flag it; never collapse two findings into one.
- **Every finding gets a row in *Your calls*** — including the ones cut from the prose above. Not
  one dropped for looking minor, not one merged away for resembling another. The cap shortens what
  gets explained; it never shortens what gets listed. Editorial filtering is how the best finding in
  a run gets lost.

---

## Fixing

Only after an explicit yes, and only on the items called `do`. Not "the plan" — the items.

1. **One item at a time, smallest first.** Each becomes its own commit whose message says what was
   wrong, not just what changed (`sources.md` § 5, § 6). A single giant commit can't be undone
   selectively, which is the whole reason for the rule.
2. **Reproduce before you fix.** For anything reported as broken, show the failure first, then the
   same check passing. A fix whose original failure was never reproduced is a guess.
3. **Run the project's real verification command after each one.** Not a claim that it passes —
   the output.
4. **Branch, push, open a pull request.** The owner reviews the diff, not a description of it.
5. **Never widen the change.** Fix what the finding named. A refactor riding along in a fix commit
   breaks rule 1 and hides the fix.
6. **Report what actually happened** — what landed, what you skipped and why, what failed, with the
   output. Never a fix reported as applied without the diff behind it. This skill exists because
   that happens; do not do it here.

## The ledger

After the calls are made, write one file into the folder resolved at Step 1 —
`<that folder>/YYYY-MM-DD-linus-review.md`.

**Format:** a table of numbered findings — the owner's call beside each (`do` / `skip` / `later`),
a result column filled in as work lands (commit or PR link, or why it didn't), and one line on the
reason behind every skip. Thirty lines at most. Newest file wins; never edit an old ledger, write a
new one.

Step 1 reads it. That read is what stops the next review re-raising something already declined, and
what makes an approved fix that never landed impossible to lose quietly.
