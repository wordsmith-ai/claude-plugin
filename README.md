# Wordsmith for Claude

Wordsmith is a legal AI platform for in-house legal teams. This plugin connects Claude to your Wordsmith
workspace and adds skills for four everyday legal workflows:

| Skill | What it does |
| --- | --- |
| `contract-review` | Reviews a contract against one of your playbooks or your own instructions, lists the findings, and produces a tracked-changes Word redline |
| `legal-research` | Searches case law and legislation in public legal databases (UK, US, EU, New Zealand and 30+ legislation jurisdictions) and reads the full text |
| `draft-from-blueprint` | Drafts a Word or PDF document from one of your blueprints, filling its placeholders from values, source documents or a past conversation |
| `file-into-repository` | Creates repositories and folders, files documents into them so they are searchable, and searches what is filed |

The plugin adds the Wordsmith MCP server at `https://api.wordsmith.ai/mcp`. Its tools also cover asking
your Wordsmith agents, tasks, reports, sharing and usage. See the [Wordsmith MCP documentation](https://docs.wordsmith.ai/mcp).

## Requirements

- A Wordsmith account. Your organisation's Wordsmith administrator may need to enable connected apps.
- Claude on any surface that supports plugins: claude.ai, Claude Desktop, Cowork or Claude Code.

## Getting started

1. Install the plugin.
2. The first time Claude calls a Wordsmith tool, Claude asks you to connect. Sign in to Wordsmith and
   approve the access listed on the consent screen.
3. Ask Claude for a task, for example: "Review the attached NDA against our NDA playbook, acting for the
   customer, and give me a redline of the high-severity issues."

You can disconnect at any time from Claude's connector settings.

## Privacy

The plugin itself stores nothing and runs no code on your machine. It contains skill files and the
address of the Wordsmith MCP server.

When you use it, Claude sends Wordsmith the tool calls needed for your request: the files you choose to
add, the text of your questions and instructions, and the ids of Wordsmith objects such as playbooks,
reviews and repositories. Wordsmith returns results to Claude, which shows them in your conversation.
Claude reaches Wordsmith only through the MCP server listed above, and the plugin sends data nowhere else.

- **What Wordsmith collects and why:** account, usage and the content you send, to provide the service.
- **Where it is stored:** in your organisation's Wordsmith data region (EU or US).
- **Who it is shared with:** Wordsmith uses AI models from OpenAI, Anthropic and Google to process
  requests. Those providers do not train on your data.
- **Retention:** content stays in your Wordsmith workspace until you or your organisation delete it, under
  your organisation's Wordsmith agreement.
- **Access:** each tool acts as you, with your Wordsmith permissions, limited to the access you approved.

Read the full [Wordsmith privacy policy](https://www.wordsmith.ai/privacy-policy) and
[security overview](https://www.wordsmith.ai/security). Questions: [support@wordsmith.ai](mailto:support@wordsmith.ai).

## Support

Email [support@wordsmith.ai](mailto:support@wordsmith.ai).

## License

[MIT](LICENSE)
