# Sats4AI Tools — pack instructions

This workspace has a **Sats4AI** agent that can buy AI capabilities on demand — voice,
transcription, translation, image generation, and phone / SMS / fax — and pay for each
one with a small Lightning invoice (the L402 standard). There is no account and no API
key; billing is one payment per call, in sats.

Mention `@sats4ai` (or use a keyword like *transcribe*, *translate*, *voiceover*, *image*,
*sms*, *fax*, *call*) and describe what you need. The agent will look up the price, tell
you the cost in sats, run the tool, and return the result.

Payment happens through a Lightning wallet MCP paired in this pack. Keep a budget on that
wallet — the agent is told to state each cost before spending, but a wallet-side budget is
the real backstop.
