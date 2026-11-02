---
name: stratos
description: Pull the company's committed strategy — buyer, positioning, pricing, the bets currently open and the moves the founder has sanctioned — before scoping, specing, planning, or building any feature, and before writing any customer-facing copy. Use whenever the user asks you to build, scope, spec, plan, prioritise, price, position, or name something, or asks what to work on next.
---

# Build against committed strategy, never invented strategy

StratOS holds this company's committed decisions: who they sell to, how they price, what
they claim, what they are currently betting on, and which moves a human has sanctioned.
Each one was reviewed and committed by a person on a date, and each one can go stale.

You have access to it. Use it instead of guessing.

## The rule

**Before you scope, spec, plan, prioritise, price, position, or name anything — read the
committed position first.**

```
get_strategy_context()
```

Free, deterministic, no arguments. It returns everything the company has committed, the bets
currently open, the moves the founder sanctioned, what is waiting on a person, and —
critically — **what has NOT been decided.**

**You do the matching.** It does not guess which bet the request touches; you are holding the
user's sentence, so that judgment is yours. **Name the bet you picked**, with its kill criterion
and read date, so the person can correct you if you picked wrong.

Then act on what came back:

| What came back | What you do |
|---|---|
| A bet the request serves | Name it, its kill criterion and its read date, before you plan. The work serves that bet. |
| A committed read (positioning, pricing, customer profile) | Use it verbatim. It is the company's committed language, not a first draft. |
| `stale: true` on a read you need | Say so and name the cause (`stale_cause_kind`). Do not build on it silently. |
| A bet with `past_read_date` | Say so. A read date that passed with no reading is an open question, not a success. |
| A bet with `needs_verdict` | A reading crossed its threshold and a person has not ruled. Do not treat the bet as won or lost. |
| A move in `moves[]` that matches the request | That is sanctioned work. Read it with `read_move`; if it is `approved`, call `start_move` before you build. |
| A move in `retired_moves[]` you were about to do | A refresh retired it. Stop and re-read; do not build a retired move. |
| Something in `held[]` that bears on the work | Surface it. A person is deciding it, and it may decide your work. |
| The outcome you need is in `absent[]` | **Stop. Do not invent it.** See below. |

## Building a feature

1. `get_strategy_context()`, and name the bet this serves.
2. If the work is a sanctioned move, `read_move(action_id)` and then `start_move(action_id)`.
   If it is a committed spec, `read_build_spec(artifact_id)`. **Read the header before any
   prose:** `agent_executable: false` means stop and print the refusal.
3. **Confirm the dependencies the spec names actually exist in this repository before you
   write anything.** If they do not, say so and stop. You may be in the wrong repo.
4. **Read what the company committed to do now.** `header.upstream.mode` is one of build,
   instrument, data_pull, research, no_build, and `mode_means` says it in a sentence. When
   `capability_is_conditional` is true the capability is **not yet earned**: the work is the test
   in `gates`, and you do not build the capability itself.
   **Then read what this spec decided.** `header.upstream.spec_decision` says what the spec does
   with that capability, and `means` is the instruction. `narrow` means build the bounded slice the
   sections describe, even while the capability is conditional: that is not a contradiction, the
   slice ships before the evidence and the rest waits on the gates. `sequence` means build only
   what must come first; `supersede` means build what the sections describe instead; `kill` means
   build none of it. When it is null an open question says so: ask the person what to build now.
   Say all of this to the person before you plan.
   **Then build to the gates.** Each gate is a complete test, as data: `test` (what to run),
   `pass`, `kill_criterion`, `inconclusive` (the middle band and the move it triggers),
   `threshold_basis` (why the bar sits there), `count` (the typed n of m, when there is one) and
   `read_date`. Implement those fields as written, not a paraphrase of the prose. A field that is
   `null` was not committed: ask, do not fill it. A gate that is `resolved`, `archived` or
   `past_read_date` is something to raise, not to build past.
5. **`do_not` is a list of refusals, and so is the spec's non-goals section.** Each `do_not`
   entry is something the company is holding back, with what releases it (`until`). The non-goals
   in the sections can list more than `do_not` does: read both, and build neither, even if other
   prose seems to ask for it. If the work needs one, stop and say which.
6. Turn each gate's `pass`, `kill_criterion` and `inconclusive` into a check that can fail, then
   each acceptance line in the sections. The spec's `open_questions` are
   gaps the document did not close: **do not implement them.**
7. `scope_in` is empty because StratOS does not know this repository. Ask the person which paths
   you may modify; that is a question for them, not a reason to stop for good.
8. Propose the plan from the sections. The build order is not served as data, so the order is
   yours to propose and the person's to approve. If you split the work across parallel sessions or
   tickets, every ticket carries the same `gates` and `do_not`, and no `open_questions` entry
   becomes a ticket.

## Before a spec is committed

If the person has a **staged** spec and asks you to check it against the code, that is the one
check StratOS cannot run:

```
post_preflight_finding(artifact_id, kind, claim, evidence)
```

One typed finding per call — `confirms`, `complicates` or `blocks` — with a one-sentence claim
and a `path/file.ts:line`. Never code. A finding never blocks the commit; the founder resolves
it. For a spec, `commit` requires the `preflight_id` this returns.

## Committing any read

`list_committed` also returns `staged`: the reads waiting for a decision, each with its
`artifact_id`, what it is and a one-line summary. `commit(artifact_id)` works for any of them;
only a spec (`needs_preflight: true`) needs a `preflight_id`. The first call never commits: it
stops and asks the person, and returns a `confirm_token` to send back on the retry once they
have said yes. **Never call `commit` until the person you work for has said to.** The answer says
what landed on the board (bets and the actions that test them) and the one next step: tell the
person both. If it says the read is `still_being_prepared`, it did not come back complete and
StratOS is preparing it again; commit the new version when it lands.

## After the work

Report the move, and the number if you measured one:

```
post_move_outcome(action_id, disposition, learning, outcome?, reading_value?)
```

If you measured the bet's threshold directly, outside a move:

```
post_bet_reading(bet_id, value, observed_at, note)
```

Post only what you actually measured — never an estimate, never a target, never a number you
inferred. A reading is evidence a person acts on; it does not resolve the bet, and a crossed
threshold asks the founder for the verdict. An unmeasured reading corrupts a decision record.

Between turns, `get_changes(since)` tells you what moved — a read staled, a bet's terms
changed, a move retired — without re-reading everything.

## What you must not do

**Do not invent the buyer, the price, the positioning, or the priority.** These are
committed decisions with evidence behind them, not details to be plausible about. If they
are missing, they are missing on purpose, and the honest move is to say so.

If what you need is in `absent[]`, or nothing is committed at all, stop and say:

> This company has not committed a strategy in StratOS yet. Commit your positioning and
> pricing there, then pull the spec.

Do not offer to interview the user and build one yourself. That is the thing StratOS exists
to do properly, and a guess wearing a spec's formatting is worse than an honest gap.

## Do not paraphrase committed language

Positioning and pricing come back as the company's committed words. In landing page copy,
sales material, release notes or a README, **use them as written.** If they do not fit the
context, say why rather than rewriting them — a claim that drifts in a rewrite is how a
company ends up with four different descriptions of itself.

## Not connected yet

The server is remote; nothing installs on your machine. Mint an agent key in **Settings →
Agent keys** (shown once), then:

```
claude mcp add --transport http stratos https://stratos-cloud.onrender.com/mcp \
  --header "Authorization: Bearer strat_sk_…"
```

Other MCP clients take the same URL and header; see `reference/COMMANDS.md`. A key minted by a
read-only seat sees the read tools and none of the write tools.

---

Full tool surface: `reference/COMMANDS.md` · Vocabulary: `reference/CONCEPTS.md`
