# Evidence base — what Linus Torvalds and the kernel process docs actually say

Every quote below was checked against its source on 2026-08-19. Where a source could not be
fetched, the section says so in its own words — read the section, not this line. Cite a section
number in a finding, or mark the finding as the reviewer's own read. **Never invent a quote and
never paraphrase one into quotation marks.** What could not be attributed at all was dropped rather
than softened — see § Dropped at the bottom, and keep that habit.

A warning that applies to this whole file: it is kernel doctrine, written for C, for an operating
system, by people maintaining a thirty-year-old codebase. Most of it carries over. Some of it does
not. A reviewer that forces § 7 onto a React component has stopped reviewing and started cosplaying.
Say when a rule doesn't fit.

---

## 1. Data structures decide everything

> "I will, in fact, claim that the difference between a bad programmer and a good one is whether he
> considers his code or his data structures more important. Bad programmers worry about the code.
> Good programmers worry about data structures and their relationships."

— Linus Torvalds, git mailing list, 2006-07-27 · https://lwn.net/Articles/193245/

**How to use it.** Before writing a single finding about a function, ask what shape the data is in.
A review that lists ten branch-level nits and misses that the whole problem is one badly chosen
structure has failed. This is the highest-ranking lens for a reason.

## 2. Good taste — the special case that shouldn't exist

> "But I want you to understand that sometimes you can see a problem in a different way and rewrite
> it so that a special case goes away and becomes the normal case, and that's good code."

— Linus Torvalds, TED talk, 2016, at roughly 14:10 ·
https://www.ted.com/talks/linus_torvalds_the_mind_behind_linux

**Verification note:** the TED transcript would not fetch directly; this wording is as reported from
the talk in multiple independent write-ups of that segment. Treat it as reliably attributed but
**not** verified character-for-character, and say so if you quote it in a report.

The example he gives: removing an item from a singly linked list. The tasteless version needs an
`if` for the head of the list. The good version uses a pointer to a pointer, and the head stops
being special at all. Same behavior, one fewer branch, one fewer thing to get wrong.

**How to use it.** Every `if` that exists to handle "the first one" / "the empty one" / "the last
one" is a candidate. Ask whether a different shape makes it disappear. When it does, that finding
outranks every style finding in the report.

## 3. Never break the user

> "If a change results in user programs breaking, it's a bug in the kernel."
> "We never EVER blame the user programs."
> "WE DO NOT BREAK USERSPACE!"

— Linus Torvalds, LKML, 2012-12-23 ·
https://lkml.iu.edu/hypermail/linux/kernel/1212.2/03058.html

**How to use it.** Translate "userspace" to whatever depends on the thing being changed: a deployed
page, a saved row belonging to a paying customer, a public URL someone bookmarked, an API another
build calls. Anything in the diff that changes behavior people already rely on is the top finding,
above correctness, above design. And the same rule about blame applies: if a change breaks something
downstream, it is this change's problem, not the downstream code's.

## 4. Functions do one thing

> "Functions should be short and sweet, and do just one thing. They should fit on one or two
> screenfuls of text (the ISO/ANSI screen size is 80x24, as we all know), and do one thing and do
> that well."

> "if you need more than 3 levels of indentation, you're screwed anyway, and should fix your
> program."

— Linux kernel coding style · https://www.kernel.org/doc/html/latest/process/coding-style.html

**How to use it.** Deep nesting is a symptom, not the disease — the doc says *fix your program*, not
*reformat it*. When you find it, trace back to what made it necessary. That's usually § 1 or § 2
again.

## 5. One logical change per patch

> "Separate each **logical change** into a separate patch."
> "each patch should make an easily understood change that can be verified by reviewers. Each patch
> should be justifiable on its own merits."

— Linux kernel, submitting patches ·
https://www.kernel.org/doc/html/latest/process/submitting-patches.html

**How to use it.** Two things to look for. A commit carrying unrelated work riding along, which
means it can't be reverted without losing the other thing. And the opposite failure, which is the
more common one here: work that was done and never landed — committed but not pushed, pushed but
never merged, a branch sitting behind an unreviewed pull request. Both are findings.

## 6. Describe the problem, not the diff

> "Describe your problem...Convince the reviewer that there is a problem worth fixing and that it
> makes sense for them to read past the first paragraph."
> "the patch (series) and its description should be self-contained."

— Linux kernel, submitting patches (same URL as § 5)

**How to use it.** A commit message or pull request body that describes *what changed* and never
*what was wrong* is a real finding, not a nit: it is the difference between a repo you can pick back
up in six months and one you can't. Applies with full force to a build where the author will be the
only maintainer.

## 7. Comments explain why, code explains how

> "Comments are good, but there is also a danger of over-commenting. NEVER try to explain HOW your
> code works in a comment: it's much better to write the code so that the **working** is obvious,
> and it's a waste of time to explain badly written code."

— Linux kernel coding style (same URL as § 4)

**How to use it.** A comment explaining a confusing block is a marker for the real finding, which is
the confusing block. Report the block, not the comment.

## 8. Centralized exits — handle errors once

> "The goto statement comes in handy when a function exits from multiple locations and some common
> work such as cleanup has to be done. If there is no cleanup needed then just return directly."

The reasons it gives are three separate list items, quoted here as three:

> - "unconditional statements are easier to understand and follow"
> - "nesting is reduced"
> - "errors by not updating individual exit points when making modifications are prevented"

— Linux kernel coding style (same URL as § 4)

**How to use it.** `goto` is C and does not carry over. The reasoning does, exactly: cleanup and
error handling duplicated across many exit points will drift, and someone will add the ninth exit
and forget the cleanup. In other languages the same job is done by `try/finally`, a wrapper, or one
error path every branch falls into. Silent `catch` blocks are the worst version of this and always
worth a finding.

---

## Dropped

- **"Talk is cheap. Show me the code."** Widely attributed to Linus on LKML around 2000. The archive
  message could not be found to confirm the wording or the date, so it is not in this file. The idea
  survives anyway as lens 3 of the review, which needs no quote to justify it.

Keep this section. A visible list of what got cut for lack of evidence is what makes the rest of the
file worth trusting.
