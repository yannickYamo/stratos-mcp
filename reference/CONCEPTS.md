<!-- Hand-maintained (2026-09-19). Source of truth for these words: stratos-cloud/src/help/PRODUCT_HELP.md and the
     tool descriptions in src/core/mcp-tools.ts. There is no generator yet; edit this in the same slice as the words. -->

# The twelve words, and what they actually mean here

Several of these words mean something else in software. If you answer from your priors you
will be fluent and wrong, and the person reading you will have no way to tell. Answer from
this file or say you do not know.

## committed

A human read the artifact and accepted it, on a date. Committed strategy is the company's
position: it is quotable, it is what other work rests on, and it is the only thing that
leaves StratOS.

## staged

Generated and waiting for a human. **Staged is not committed and never leaves.** If you are
told something is staged, the answer is "a person still has to look at this," not "close
enough."

## stale

⚠️ **Not cache invalidation.** A committed read is stale when something it rested on
changed underneath it — a bet resolved, an upstream decision moved. The read is not wrong,
and it has not expired. It is **unreviewed against what changed**, and it names what
changed.

You may use a stale read. You must say it is stale and name the cause first.

## bet

A claim the company is testing on purpose, with a kill criterion and a read date, committed
by a human. Not a task, not a roadmap item, not an idea. A bet can be wrong — that is what
it is for.

## kill criterion

The condition, written in advance, under which the bet is abandoned. It is written before
the evidence arrives so it cannot be moved once the evidence is inconvenient. Quote it
exactly. Do not soften it, and do not restate it as a goal.

## read date

When the bet gets looked at. Not a deadline and not a ship date — the date a human decides
whether the claim held. A bet whose read date has passed with no reading is an open
question, not a success.

## cascade

When a decision is committed, the committed reads that rest on it are marked for review.
This is why a product decision is never only a product decision: pricing, positioning, the
landing page and the sales material can all rest on the same claim.

The cascade marks work for review. **It never silently rewrites anything.**

## held

Drafted and waiting on a person, deliberately. Held is a decision StratOS declined to make
on the founder's behalf. If something is held and it bears on what you were asked to do,
say so — do not route around it.

## move

A concrete next action the product proposed against a bet, with what success and failure would
mean for that bet. A move has a status. Only a **sanctioned** move — approved by the founder, or
already in flight — is ever served to an agent. Drafted, set aside and rejected moves do not
leave.

## sanctioned

The founder approved it. Approval is the founder's verb; `start_move` is yours. Saying you
started a move does not approve it, and reporting an outcome does not resolve the bet it tests.

## reading

A measured number against a bet's typed threshold, at the time the world produced it, with the
system that measured it. The threshold was typed before the work, so the reading scores itself
and the server writes the progress line. A reading is evidence a person acts on. It never
resolves the bet; when it crosses the threshold the bet is marked **needs verdict** and waits.

## divergence

The frozen prose of a committed read may name a bet whose terms the founder has since changed,
whose question was superseded, or which resolved *against* the text. The prose is never edited
(the record is the record); `divergence[]` on the read tells you which bets, and for those the
live context wins.

## challenge

A downstream test failed against a committed read, and the failure was raised upward against
it. It is attention, not a verdict: the read's claims are under review, not settled, until a
person rules.

---

## Evidence tiers

Claims carry how they are known:

- **Known** — first-party evidence exists
- **Assumed** — reasoned, no first-party evidence
- **Unresolved** — an open question, named rather than filled in

Carry the tier when you carry the claim. An Assumed claim presented as Known is the failure
this vocabulary exists to prevent.
