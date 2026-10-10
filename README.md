# IssueBadge agent skills

Skill files that teach an AI agent how to connect to [IssueBadge](https://issuebadge.com) and exchange data with it: issue and verify certificates and badges, list templates, download PDFs, revoke, and manage dynamic QR codes, over the MCP server (OAuth 2.1 or API key), the REST API, or the agent CLI.

## Install

Claude Code (one step, 2.1.275 or later):

```
/plugin install issuebadge --marketplace issuebadge2026/issuebadge-skills
```

Any agent that supports the Agent Skills format (Codex, Cursor, Hermes, OpenClaw, Viktor and others):

```bash
npx skills add issuebadge2026/issuebadge-skills --skill issuebadge
hermes skills install issuebadge2026/issuebadge-skills/skills/issuebadge
```

Viktor: upload the `skills/issuebadge-viktor` folder under Skills, Upload skill, or install it from the Viktor Skills Marketplace once listed.

Or copy `skills/issuebadge/SKILL.md` into your agent's skills directory (`.claude/skills/issuebadge/SKILL.md`, `.codex/skills/issuebadge/SKILL.md`, or the equivalent).

## Contents

- `skills/issuebadge/SKILL.md`: how to connect (MCP + OAuth 2.1, API key, CLI), the issue workflow, how to get data back out (issued list, history, PDFs, webhooks, QR codes), and the rules an agent should follow before issuing.
- `skills/issuebadge-viktor/SKILL.md`: the same workflow written for Viktor, including the Integrations, Add Custom steps for the MCP server and the REST API.
- `.claude-plugin/`: makes this repository a Claude Code marketplace; the plugin bundles both skills and the hosted MCP server with OAuth 2.1 sign-in (no API key).

## Related

- MCP server: https://github.com/issuebadge/mcp-server
- Agent CLI (PyPI `cli-anything-issuebadge`): https://github.com/issuebadge2026/cli-anything-issuebadge
- Laravel notification channel: https://github.com/issuebadge2026/laravel-notification-channel-issuebadge

MIT License.
