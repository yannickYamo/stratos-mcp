# StratOS for coding agents

**Speed is solved. Direction is not.**

Your agents are fast now. Ask Claude Code, Cursor or Codex to build billing and it will pick
the customer, set the price and write the landing page copy on the way, without asking you.
It all reads fine. It is also made up, and it is not what you decided. Six months of that and
you have a product that works, that nobody asked for, and a lot less runway. Most startups
die exactly there.

StratOS is a strategy operating system for founders. You work out, with it, who you are
really building for, what they will pay, where you stand against everyone after the same
customer, and which bets are worth the next month. Every decision you commit stays with its
evidence and its date, gets flagged the moment something under it changes, and goes to every
agent you run. The agents still do the work. StratOS makes sure it is the right work, and that
the direction holds while they build.

That is what a first strategic hire gives you, years before you can afford one: fewer months
spent building the wrong thing, less money spent learning what a customer would have told you
for free, and a product that lands where your customers already are, while the others are
still guessing. Prompt packs make your agents fast. StratOS makes them right.

This repository is the front door for your agents: one remote MCP server and one skill. The
skill makes an agent read your committed direction before it scopes, specs, prices or names
anything, build from it in your own words, and tell you plainly when something has not been
decided yet instead of deciding it for you.

- Product: [getstratos.ai](https://getstratos.ai) · the page for your agents: [getstratos.ai/?audience=agents](https://getstratos.ai/?audience=agents)
- The app: [app.getstratos.ai](https://app.getstratos.ai)
- Setup page written for the agent itself: [app.getstratos.ai/install.md](https://app.getstratos.ai/install.md)

## Connect

Nothing installs on your machine; the server is remote. Sign in at
[app.getstratos.ai/settings](https://app.getstratos.ai/settings), open **Agent keys**, create a
key and copy it. It is shown once. Paste it into the command yourself, never into a chat.

Claude Code:

```
claude mcp add --transport http stratos https://stratos-cloud.onrender.com/mcp \
  --header "Authorization: Bearer <key>"
```

Any other MCP client that speaks streamable HTTP takes the same two facts, the endpoint
`https://stratos-cloud.onrender.com/mcp` and the header `Authorization: Bearer <key>` on every
request. A typical JSON form:

```json
{ "mcpServers": { "stratos": {
  "url": "https://stratos-cloud.onrender.com/mcp",
  "headers": { "Authorization": "Bearer <key>" } } } }
```

Then call `get_strategy_context`. You are connected only when it returns the company's
committed reads. A 401 is the key, not the server. An empty result on a working key means the
person has committed nothing yet; the agent says that plainly and does not write a strategy
for them.

Witnessed end to end with Claude Code. Cursor and Codex take the same block and have not yet
been witnessed against this server.

**Two kinds of key.** A key created by the founder sees every tool. A key created by a read-only
seat sees the read tools and none of the write tools; the write tools are not even listed to
it. Signing in with OAuth instead of a key is being built and is not available yet.

## What the agent gets

`get_strategy_context` is free, deterministic and takes no arguments. It returns:

- **the buyer** the company committed to, in its committed words
- **the pricing**, and what sits in which tier
- **the bets currently open**, each with the kill criterion and read date a person set, and
  the server's own progress line against the threshold
- **the moves a person sanctioned**: approved or in flight, nothing drafted or set aside
- **what is stale**: the decisions something changed underneath, and what changed
- **what is held**: waiting on a person, possibly the thing that decides the work
- **what is absent**: the decisions nobody has made, so the agent does not make them

On the way back the agent can start a sanctioned move, report its outcome, check a staged
spec against the code before a person commits it, and post a measured number against a bet's
own threshold. Every write stops and asks the person first.

You can read everything your agent reads, in the app, and share any one committed read with
anyone by a link.

## Why not a strategy doc in the repo?

You may already keep one: a positioning page in Notion or Obsidian, a pricing table in the
README, a few skills that remind the agent who the customer is. Keep them. What none of them
enforces is the shape of a decision, the grade of a claim, or what happens upstream when a
bet fails. A doc cannot tell your agent that the price it is reading was superseded on Tuesday,
that the buyer it is quoting was an assumption nobody tested, or that the question it is about
to answer has never been decided at all. StratOS holds each decision as typed, committed state
with its evidence and its date, and tells the agent all three.

## What it refuses to do

It does not interview you and invent a strategy. If nothing is committed it says so and stops.
A guess wearing a spec's formatting is worse than an honest gap.

It does not commit anything on its own. `commit` stops and asks on the first call, and a spec
needs a preflight finding from the repository before it can be committed at all.

It does not sound more certain than its evidence. Every claim carries how it is known: known,
assumed, or to verify.

It does not expose how StratOS reasons. This repository documents the front door: tool
signatures, scopes, refusals and vocabulary. The engine stays where it is.

## The skill

`SKILL.md` is a Claude Code skill. It loads when a request touches strategy and tells the
agent to read the committed position before it scopes, specs, plans, prices, positions or
names anything, which tool to call for each step, and what to say when the answer is a gap.

Install it in a project:

```
git clone https://github.com/yannickYamo/stratos-mcp .claude/skills/stratos
```

or copy `SKILL.md` and `reference/` into `.claude/skills/stratos/` by hand. The skill is a
manual; the MCP connection above is what gives it something to read.

## Contents

| File | What it is |
|---|---|
| `SKILL.md` | The skill. Hand-written. |
| `reference/COMMANDS.md` | Every tool: arguments, returns, scopes, costs and refusals, plus how to connect. Generated from the server's own tool registry, so the list and the descriptions are the ones the model is served. |
| `reference/CONCEPTS.md` | The words an agent must not get wrong: committed, staged, stale, bet, kill criterion, read date, cascade, held, move, sanctioned, reading, divergence, challenge. Hand-written. |

## Keeping this true

`reference/COMMANDS.md` is a build artifact of the server: when a tool is added, renamed or
re-shaped, the server's build regenerates the file, including every count in it, and the
change lands here. Do not edit it by hand; the next build overwrites it. `SKILL.md`,
`CONCEPTS.md` and this README are written by people and carry no tool counts, so they cannot
drift on a number.

Something wrong, unclear or missing? Open an issue on this repository.

## License

MIT. See `LICENSE`.
