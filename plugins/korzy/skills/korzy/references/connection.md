# Connect to Korzy

The plugin connects to Korzy's MCP server at `https://api.korzy.co/mcp` (declared in `mcp.json`, and in `.mcp.json` for Claude Code and Codex). The first time your client connects, it opens a Korzy sign-in in your browser; approve access and the client keeps its own token. No API key is needed.

## Account and plan

Korzy needs an account with an active plan; there is no free trial.

1. Sign up at https://app.korzy.co/auth/signup.
2. Choose a plan. Professional and Business include MCP and API access: https://korzy.co/pricing/#plans.
3. Pay with Stripe Checkout. If you have a promotion code, enter it at checkout.

Without a plan, requests return `402` with `subscription_required`. Manage your plan, credits and API keys at https://app.korzy.co/account.

## Claude Code

From the extracted `korzy-plugin` directory:

```sh
claude plugin marketplace add ./
claude plugin install korzy-research@korzy
```

Or load it for one session with `claude --plugin-dir ./plugins/korzy`. Run `/mcp`, choose `korzy` and sign in. Invoke `/korzy-research:korzy` for the parcel workflow.

## Codex and ChatGPT

From the extracted `korzy-plugin` directory:

```sh
codex plugin marketplace add ./
codex plugin add korzy-research@korzy
```

Restart the session and sign in when prompted. In the ChatGPT desktop app, install Korzy from the Plugins Directory. Invoke `$korzy` in a new session.

## Hermes

Copy the extracted `plugins/korzy` folder to your profile's `plugins/korzy-research` directory (default `~/.hermes/plugins/korzy-research`), then run:

```sh
hermes plugins list
hermes plugins enable korzy-research
```

Use `skills_list` to find the Korzy skill, then `skill_view` to load it.

## API keys

For automation or a client without browser sign-in, create a key at https://app.korzy.co/account with `mcp:read` (and `api:read` for REST) and follow https://korzy.co/docs/mcp/#connect. Keep keys in a secret manager, never in a prompt or a committed file, and don't combine a key with sign-in.

## Verify

Ask the agent to call `list_datasets`. A working connection returns datasets with coverage and dates. Signing in to the Korzy website doesn't connect your agent; the agent needs its own sign-in or key. The first request after a quiet period can take a little longer.

Client guides: [Claude Code plugins](https://code.claude.com/docs/en/plugins), [Codex plugins](https://developers.openai.com/plugins/build/plugins), [Hermes plugins](https://hermes-agent.nousresearch.com/docs/developer-guide/plugins).
