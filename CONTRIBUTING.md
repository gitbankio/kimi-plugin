# Contributing to gitbankio/kimi-plugin

Thanks for your interest in contributing to the Gitbank Kimi Code plugin.

## What is this repo

This repo is a [Kimi Code CLI](https://kimi.com/code) plugin that connects Gitbank's MCP server to your coding session.

It contains:
- `kimi.plugin.json` - plugin manifest (MCP server URL + skills config)
- `skills/gitbank/SKILL.md` - agent instructions for using Gitbank tools

## Good contributions

- Improve the SKILL.md instructions (better tool usage examples, clearer explanations)
- Add new skill files for specific workflows (e.g. bounty review, project audit)
- Fix typos or unclear wording in README or skills
- Add config examples for other MCP clients

## Workflow

1. Open an issue describing the change before opening a PR
2. Fork the repo, make changes on a branch
3. Open a PR with a clear description of what changed and why

## Rules

- No em dash in any file
- No Replit or internal tooling mentions
- SKILL.md changes must be tested in a real Kimi Code session before submitting
- Keep instructions concise; the agent reads SKILL.md on every session start

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
