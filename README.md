# Korzy plugin

Parcel research for Claude Code, Codex and Hermes. Find a US land parcel by street address or coordinates, read its record, enrich up to 25 places at once, add owner names only when you ask, check nearby utilities, environmental layers, logistics, power plants, grid capacity and data center projects, and ask questions answered from Korzy data. The plugin runs no local code: it connects your agent to Korzy's MCP server at `https://api.korzy.co/mcp`, and you sign in with your Korzy account the first time it connects.

Korzy requires a paid plan. Sign up at [app.korzy.co](https://app.korzy.co/auth/signup) and see [pricing](https://korzy.co/pricing/).

## Install

Claude Code:

```sh
claude plugin marketplace add ChristianBako/korzy-plugin
claude plugin install korzy-research@korzy
```

Codex: install Korzy from the Plugins Directory in the ChatGPT desktop app, or clone this repository and run:

```sh
codex plugin marketplace add ./korzy-plugin
codex plugin add korzy-research@korzy
```

Hermes and other clients: see [connection setup](plugins/korzy/skills/korzy/references/connection.md). The plugin itself is in [`plugins/korzy`](plugins/korzy).

Documentation: [korzy.co/docs/mcp](https://korzy.co/docs/mcp/). Support: [korzy.co/contact](https://korzy.co/contact/?topic=Plugin+support). [Privacy](https://korzy.co/privacy/) and [terms](https://korzy.co/terms/).

Released under the MIT License. Korzy data and the API are provided under the [Korzy Terms of Service](https://korzy.co/terms/).
