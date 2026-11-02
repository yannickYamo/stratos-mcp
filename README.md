# StratOS for coding agents

**Your coding agent has the whole internet. It does not have your strategy.**

Claude Code, Cursor and Codex will happily scope your billing feature. They pick a buyer,
invent a price point and write a landing page headline. All of it plausible, none of it yours,
none of it the thing you already decided and tested.

StratOS is where a founder commits strategy: who they sell to, how they price, what they
claim, what they are betting on, and which moves a person has sanctioned. This repository is
the front door for agents: one remote MCP server and one skill that makes the agent read the
committed position before it builds, and say so when nothing is committed.

- Product: [getstratos.ai](https://getstratos.ai) · app: [app.getstratos.ai](https://app.getstratos.ai)
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

## What it refuses to do

It does not interview you and invent a strategy. If nothing is committed it says so and stops.
A guess wearing a spec's formatting is worse than an honest gap.

It does not commit anything on its own. `commit` stops and asks on the first call, and a spec
needs a preflight finding from the repository before it can be committed at all.

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

## License

MIT. See `LICENSE`.
