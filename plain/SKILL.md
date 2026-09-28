---
name: plain
description: Rewrite the previous response as one-line bullets in plain, slightly-technical English — readable in about ten seconds. Use when invoked as /plain, or when the user says condense that, shorter, tldr, too long, simplify that, plain English, say that again but simpler, too much jargon, or I don't follow. Also use when invoked at the start of a request (e.g. "/plain then answer X") to write the answer this way the first time instead of rewriting afterward.
license: MIT
---

# Plain

Rewrite so a sharp person outside the specialty gets it in one read.

## Slightly technical, not non-technical

Keep real technical terms that name real things: git, rebase, cron, PAT, connector, schema. Those are precise and shorter than the alternative.

Cut the writerly vocabulary layered on top of them. The problem is never the domain word — it's the ornamentation. "Load-bearing fact" is ornamentation. "The clone worked" is the fact.

Never dumb down to zero technicality. Losing precision is a worse failure than sounding dense.

## Length

**One-line bullets. The whole answer readable in ten seconds.**

- Every bullet is one line. If it wraps to a third line on a normal screen, it's two bullets or it's cut.
- Six bullets maximum. Past six, the extras weren't important enough.
- No paragraphs. A shorter paragraph is still a paragraph, and that is the thing this skill exists to stop.
- One optional lead line above the bullets, only if the bullets make no sense without it.
- If it genuinely won't compress, say so in one line and give the tightest version you can — don't silently sprawl.

## Order

First sentence is the answer, decision, or number. Not the setup, not the caveat, not what you did to find out.

## Words

Every term a competent generalist wouldn't say out loud gets cut or defined in parentheses on first use. Specifically:

- Borrowed metaphors: load-bearing, surface area, first-class, orthogonal, tension, lever
- Nominalizations: "make a determination" becomes "decide", "provide clarification" becomes "explain"
- Modifier stacks more than two deep: "connector-mediated storage access" becomes "files reached through a connector"

Test each sentence: could you say it to a colleague over coffee without them pausing? If not, rewrite it.

## Keep vs cut

Keep the decision, numbers, names, dates, confidence levels, real risks, real costs, and any open question.

Cut how you got there, alternatives you rejected, tool-use narration, restating the question, and any sentence whose removal loses nothing.

Never cut a caveat that would change what the person does.

## Format

Bullets, always. No headers. Bold at most two phrases in the whole response. Tables only if the content is genuinely a grid.

## Self-check before sending

1. Count the bullets and check each is one line. Over six, or wrapping long, means cut again.
2. Read the first bullet alone. Does it answer the question? If not, move the answer up.
3. Scan for any word you wouldn't say out loud. Replace it.
4. Look for a paragraph that snuck back in. Turn it into bullets or delete it.
