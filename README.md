# Sats4AI Tools — a persona pack for [Block Buzz](https://github.com/block/buzz)

Give your Buzz agents **pay-per-use AI tools** — voice / text-to-speech, transcription,
translation, image generation, and phone / SMS / fax over the phone network — each paid
with a small **Bitcoin Lightning** invoice (the [L402](https://sats4ai.com/l402) standard).
**No login, no API key, no KYC.** Billing is one payment per call, in sats.

This pack adds one agent, **`@sats4ai`**, plus two MCP servers: the Sats4AI tool catalog and
a Lightning wallet so the agent can pay invoices autonomously.

## Why this works inside Buzz

A Buzz "agent" is a local subprocess (Goose by default, or Codex / Claude Code). Buzz
spawns it **inheriting your environment and the agent's own MCP config**, so any MCP server
this pack declares becomes available to the agent inside a Buzz workspace — no Buzz-specific
plumbing. If you already run the Sats4AI MCP in your everyday agent, it already works in Buzz.

## Requirements

- A running Buzz workspace and the `buzz` CLI.
- An agent runtime the pack targets — **Goose** (the Buzz default). Codex / Claude Code work too.
- `node` / `npx` on the machine that runs the agent (both MCP servers launch via `npx`).
- A **funded Lightning wallet** for the paired wallet MCP (see *Payment* below).

## Install

```bash
git clone <this-repo> sats4ai-buzz-pack
cd sats4ai-buzz-pack
buzz pack validate .     # structural check
buzz pack inspect .      # show the effective, resolved config
```

> Note: Buzz's CLI ships `validate` and `inspect` only — there is no `buzz install` /
> `.buzzpack` build step yet. Distribute the pack by sharing this directory (git clone),
> then load it from your Buzz desktop app / workspace.

## What's in the pack

```
sats4ai-buzz-pack/
├── .plugin/plugin.json        # manifest (id, name, version, personas, mcp_config)
├── agents/sats4ai.persona.md  # the @sats4ai agent (runtime: goose)
├── .mcp.json                  # the two MCP servers, stdio
├── instructions.md            # pack-level context shown to the workspace
└── README.md
```

## Payment

The Sats4AI server answers a request with an HTTP **402 + a Lightning invoice**; a wallet
MCP settles it, then the tool runs. This pack pairs the Sats4AI catalog with a Lightning
wallet MCP so `@sats4ai` can pay on its own:

```json
{
  "mcpServers": {
    "sats4ai":          { "command": "npx", "args": ["-y", "sats4ai-mcp"],          "env": {} },
    "lightning-wallet": { "command": "npx", "args": ["-y", "lightning-wallet-mcp"], "env": {} }
  }
}
```

**Set a budget on the wallet.** The `@sats4ai` agent is instructed to state each cost in
sats before spending, but a wallet-side spending limit is the real backstop. Fund and cap
the wallet through the `lightning-wallet` MCP's own tools (create/fund/set-budget) — its
credentials live in that wallet's local state, **not in this pack**.

## A note on keys (why the pack is safe to share)

- **No secrets are committed here.** Neither MCP command carries a key; the wallet holds its
  own credentials locally. Never paste a wallet key or seed into `.mcp.json` and commit it —
  Buzz stores config as signed events, so a key in the file becomes a key in the log.
- Buzz's `.mcp.json` does **not** interpolate `${VAR}` yet (values are passed literally). If
  a future server needs a secret, put that server in **your own agent's** MCP config (Goose /
  Codex / Claude Code) — which Buzz inherits — rather than in this shared pack.

## Use it

In any channel the agent is in, mention it:

- *"@sats4ai transcribe this voice note and translate the transcript to English."*
- *"@sats4ai what would an SMS to +33612345678 cost? If under 20 sats, send: 'Standup moved to 3pm.'"*
- *"@sats4ai turn this paragraph into a voiceover in Spanish."*
- *"@sats4ai generate an image of a lighthouse in a thunderstorm."*

The agent looks up the price, tells you the cost in sats, pays, and returns the result.
The full tool list and pricing lives at **[sats4ai.com/mcp](https://sats4ai.com/mcp)**.

## Compatibility notes (verified against Buzz source)

- `.mcp.json` is **stdio-only** in Buzz — servers are declared by `command` / `args` / `env`.
  That's why this pack uses `npx sats4ai-mcp` (the stdio proxy) rather than the remote
  `https://sats4ai.com/api/mcp` URL form that other clients (Claude Desktop, Cursor) use.
- The persona `skills:` and `hooks:` fields are parsed by Buzz but **not yet wired**, so this
  pack does not use them — MCP tools are the live surface.
- `runtime: goose` matches Buzz's default; change it to `claude` or `buzz-agent` if your
  workspace runs a different agent.

## Links

- Sats4AI: [sats4ai.com](https://sats4ai.com) · MCP: [sats4ai.com/mcp](https://sats4ai.com/mcp) · L402: [sats4ai.com/l402](https://sats4ai.com/l402)
- Buzz: [github.com/block/buzz](https://github.com/block/buzz)

## License

MIT
