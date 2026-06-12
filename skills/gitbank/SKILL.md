---
name: gitbank
description: Web3 IssueOps tools for GitHub teams - read vault balances, transactions, and project budgets, and queue vault operations on Base L2 via Gitbank MCP
---

# Gitbank

Gitbank is a GitHub-native Web3 workspace. Every GitHub user gets a soul-bound vault on Base L2, anchored to their GitHub ID. All vault operations run through \`@gitbankbot\` mentions in GitHub Issues and PRs.

**Read tools** return live data instantly. **Write tools** queue a command and return a confirm code. The user posts the code on GitHub to authorize execution. This keeps GitHub account security (YubiKey, 2FA) as the only required auth for all writes.

---

## Read tools

### \`mcp__gitbank__get_vault_balance\`

Check WETH and USDC locked in a user's Gitbank vault on Base Mainnet.

**When to use:** User asks about vault balance, locked funds, or how much is in the vault.

**Examples:**
- "What is my vault balance?" -> call with their GitHub username
- "How much does @alice have locked?" -> call with \`github_username: "alice"\`

**Returns:** vault address, WETH balance, USDC balance, Basescan link.

---

### \`mcp__gitbank__get_transactions\`

Fetch recent on-chain transactions: deposits, withdrawals, swaps, bounty payouts.

**When to use:** User asks about transaction history, recent activity, or wants to audit payments.

**Examples:**
- "Show my last 5 transactions" -> call with \`github_username\` and \`limit: 5\`
- "Did the bounty get paid out?" -> call and filter by type

**Returns:** list of transactions with type, amounts, tx hashes, Basescan links.

---

### \`mcp__gitbank__get_project_status\`

Get budget and task status for a Gitbank project.

**When to use:** User asks about project progress, remaining budget, or bounty assignments.

**Examples:**
- "How is the v2-api project going?" -> call with \`github_username\` and \`project_name: "v2-api"\`
- "How much budget is left?" -> call and show \`budget_remaining\`

**Returns:** total budget, spent, remaining, list of tasks with bounty status and assignees.

---

### \`mcp__gitbank__list_repos\`

List GitHub repositories where the Gitbank bot is installed for a user.

**When to use:** User asks which repos have the bot, or wants to verify bot installation before posting a confirm comment.

**Example:**
- "Which repos have the bot installed?" -> call with their GitHub username

**Returns:** list of installations with account, type, and manage URL.

---

### \`mcp__gitbank__check_pending\`

Poll the status of a pending write command by its confirm code.

**When to use:** After the user says they have confirmed on GitHub, call this to get the execution result and tx hash.

**Example:**
- User says "I posted the confirm comment" -> call with the confirm_code you gave them earlier

**Returns:** status (pending / confirmed / executed / expired), tx_hash, Basescan link, amounts.

---

## Write tools (GitHub confirm required)

All write tools return a \`confirm_code\`. Tell the user to post \`@gitbankbot confirm <code>\` in any GitHub issue or PR where the Gitbank bot is installed. The bot checks their GitHub identity and executes the command.

**Confirm codes expire after 10 minutes.**

### \`mcp__gitbank__request_deposit\`

Queue a deposit of USDC or WETH into the user's vault.

**When to use:** User wants to lock funds into their vault.

**Example:**
- "Deposit 100 USDC into my vault. My GitHub username is alice."

**Parameters:** \`github_username\`, \`amount\`, \`token\` (USDC or WETH)

---

### \`mcp__gitbank__request_withdraw\`

Queue a withdrawal from the vault to an external wallet address.

**When to use:** User wants to send tokens out of the vault to a wallet.

**Example:**
- "Withdraw 50 USDC to 0x1234... for user alice."

**Parameters:** \`github_username\`, \`amount\`, \`token\`, \`to_address\` (0x...)

---

### \`mcp__gitbank__request_swap\`

Queue a token swap inside the vault (USDC to WETH or WETH to USDC).

**When to use:** User wants to swap tokens without leaving the vault.

**Example:**
- "Swap 10 USDC to WETH for user alice."

**Parameters:** \`github_username\`, \`amount\`, \`from_token\`, \`to_token\`

---

### \`mcp__gitbank__request_assign_bounty\`

Queue a bounty assignment for a GitHub issue.

**When to use:** User wants to assign a bounty to a contributor for a specific issue.

**Example:**
- "Assign a 50 USDC bounty to @bob for issue #42 in alice/my-repo."

**Parameters:** \`github_username\`, \`repo\` (owner/repo), \`issue_number\`, \`amount\`, \`token\`, \`contributor\`

---

### \`mcp__gitbank__request_launch_token\`

Queue a token launch on Base mainnet via Clanker.

**When to use:** User wants to deploy a new token on Base.

**Example:**
- "Launch a token called DevFund with symbol DEV for user alice."

**Parameters:** \`github_username\`, \`name\`, \`symbol\`, \`description\`, \`link\` (optional), \`x\` (optional), \`logo\` (optional)

---

## Confirm flow

When a write tool returns a confirm_code, tell the user exactly what to do:

> "To authorize this, open this link:
> https://github.com/gitbankio/playground/discussions/4#new_comment_form
>
> And post this comment:
> @gitbankbot confirm \`<confirm_code>\`
>
> This will expire in 10 minutes. Let me know when you have done it."

After they confirm, call \`check_pending\` with the code to get the tx hash and receipt.

---

## How Gitbank bot commands work

For operations not covered by write tools, guide the user to post commands directly in GitHub:

| Goal | Command to post in GitHub |
|---|---|
| Lock funds | \`@gitbankbot deposit 10 USDC\` |
| Unlock funds | \`@gitbankbot withdraw 10 USDC to 0x...\` |
| Send to teammate | \`@gitbankbot send 5 USDC to @username\` |
| Swap tokens | \`@gitbankbot swap 10 USDC to WETH\` |
| Create project | \`@gitbankbot create project my-project budget 100 USDC\` |
| Assign bounty | \`@gitbankbot assign bounty 20 USDC to @username for #issue\` |
| Check balance | \`@gitbankbot balance\` |

---

## Setup reminder

If the user does not have a vault yet, send them to https://gitbank.io to deploy one. Vault deployment is a one-time step through the web UI. All subsequent operations use the GitHub bot or these MCP tools.

To find a repo where the bot is installed (needed for posting confirm comments), call \`list_repos\` first.
