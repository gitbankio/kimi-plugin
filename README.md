# gitbank-kimi-plugin

Gitbank plugin for [Kimi Code CLI](https://kimi.com/code). Connect your GitHub-linked Web3 vault directly to your coding session via MCP.

## What you get

After installing this plugin, Kimi Code can answer questions like:

- "What is my vault balance on Base?"
- "Show my last 10 transactions"
- "How is the v2-api project budget looking?"
- "Which repos have the Gitbank bot installed?"

No API keys, no wallet popups. The MCP server reads public on-chain data and your Gitbank workspace.

## Install

Run inside Kimi Code CLI:

```
/plugins install https://github.com/gitbankio/kimi-plugin
/reload
```

That's it. The Gitbank MCP server at `https://gitbank.io/api/mcp` connects automatically.

## Tools

| Tool | Description |
|---|---|
| `get_vault_balance` | WETH and USDC locked balance on Base Mainnet |
| `get_transactions` | Recent deposits, withdrawals, swaps, bounty payouts |
| `get_project_status` | Project budget, spent, remaining, task list |
| `list_repos` | GitHub repos with Gitbank bot installed |

All tools are read-only. No write access, no credentials required.

## Manual config (any MCP client)

Add to your `mcp.json`:

```json
{
  "mcpServers": {
    "gitbank": {
      "url": "https://gitbank.io/api/mcp"
    }
  }
}
```

Works with Kimi Code, Claude Desktop, Cursor, and any MCP-compatible client.

## Prerequisites

You need a Gitbank vault to use the vault and transaction tools. Deploy one for free at [gitbank.io](https://gitbank.io). The `list_repos` tool works for any GitHub username.

## How Gitbank commands work

All vault operations run via GitHub bot mentions, not the UI. After checking data with these tools, post the relevant command in any GitHub Issue or PR where the Gitbank bot is installed:

| Goal | Command |
|---|---|
| Lock funds | `@gitbankbot deposit 10 USDC` |
| Unlock funds | `@gitbankbot withdraw 10 USDC to 0x...` |
| Send to teammate | `@gitbankbot send 5 USDC to @username` |
| Swap tokens | `@gitbankbot swap 10 USDC to WETH` |
| Create project | `@gitbankbot create project my-project budget 100 USDC` |
| Assign bounty | `@gitbankbot assign bounty 20 USDC to @username for #issue` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT. See [LICENSE](LICENSE).
