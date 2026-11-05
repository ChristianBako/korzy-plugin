# Korzy

Parcel research for Claude Code, Codex and Hermes. Find a US land parcel by street address or coordinates, read its published record (parcel ID, land use, acreage, building, assessed value and last sale), enrich up to 25 places at once, add owner names only when you ask, and check what is nearby: water and sewer service, environmental layers, roads, rail and ports, power plants, grid capacity and data center projects. Ask a question about a place and get an answer from Korzy data with citations, or file a support ticket to report a data problem or request a feature.

## What it includes

- `skills/korzy`: the parcel research workflow for Korzy's six MCP tools (`lookup`, `enrich`, `get_record`, `ask`, `support_request`, `list_fields`), with [connection setup](skills/korzy/references/connection.md) and a [REST API reference](skills/korzy/references/api.md) for `https://api.korzy.co/v1`.
- `skills/get-started`: connecting an account and a first lookup.
- `mcp.json` and `.mcp.json`: Korzy's MCP server at `https://api.korzy.co/mcp`.

The plugin runs no local code. Your agent calls Korzy's MCP server over HTTPS with the addresses, coordinates, record IDs and questions you ask about, and nothing else; `support_request` sends the ticket message you approve. The first time it connects, you sign in with your Korzy account in the browser; the client stores its own token.

## Account

Korzy requires a paid plan; there is no free trial. Sign up at [app.korzy.co](https://app.korzy.co/auth/signup), choose a Professional or Business [plan](https://korzy.co/pricing/#plans) and pay with Stripe Checkout, where you can enter a promotion code. Requests spend your plan's credits: a lookup costs 1 credit plus 1 per nearby section (2 for terrain), enrich 1 per place (2 with owners), a record 1 and an answer from `ask` 10; listing fields and support tickets are free, and failed requests are refunded. See [credits and limits](https://korzy.co/docs/usage/).

Try: “Use Korzy to look up the parcel at 714 Mordecai Towne Pl, Raleigh, NC and tell me what's nearby.”

Documentation: [korzy.co/docs/mcp](https://korzy.co/docs/mcp/). Support: [korzy.co/contact](https://korzy.co/contact/?topic=Plugin+support). [Privacy](https://korzy.co/privacy/) and [terms](https://korzy.co/terms/).

Package version 0.4.0, plugin ID `korzy-research`. Build the downloads from the repository with `python3 web/scripts/prepare-public.py`: `korzy-plugin-0.4.0.zip` holds local marketplaces for Codex and Claude Code, and `korzy-openai-0.4.0.zip` is the OpenAI dashboard upload, with the plugin manifest at its root and review test cases in `plugin.json`. Reviewer credentials go only in the dashboard, never in the package.
