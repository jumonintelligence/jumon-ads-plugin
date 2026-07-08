# Jumon Ads Plugin

Connect Claude and Cowork directly to your ad accounts — LinkedIn, Google Ads, Meta, Microsoft Advertising, and Reddit — through [Jumon Intelligence](https://jumonintelligence.com), the ad platform access layer for AI agents.

This repository is a **Claude plugin marketplace**. It bundles:

- A reference to the **Jumon MCP server** (`https://mcp.jumonintelligence.com/mcp`) — the live tool surface Claude calls to read and act on your ad accounts
- **Skills** that teach Claude how to use those tools well: cross-platform performance review, and budget pacing / optimization judgment

Install once, then just ask Claude things like:

> "How are my campaigns doing this month?"
> "Pause the LinkedIn campaigns that are overspending"
> "Compare LinkedIn vs Meta performance for the last 30 days"

## Install

No terminal required — this works entirely from the Claude or Cowork web/desktop UI.

1. In Claude, open **Customize → Plugins**.
2. Click **Add marketplace**.
3. Paste: `jumonintelligence/jumon-ads-plugin`
4. Find **jumon-ads** in the list and click **Install**.
5. Sign in with your Jumon account when prompted — this is the same OAuth flow used to connect ad accounts in the Jumon dashboard.

Before installing, connect your ad accounts at [jumonintelligence.com](https://jumonintelligence.com) so Claude has something to read and act on.

## What's included

| Component | Purpose |
|---|---|
| `plugins/jumon-ads/.mcp.json` | Points Claude at the live Jumon MCP server |
| `plugins/jumon-ads/skills/ad-performance-review` | Cross-platform performance and pacing review |
| `plugins/jumon-ads/skills/budget-pacing-and-optimization` | Judgment for pausing, resuming, and adjusting budgets/bids |

## Skills

| Skill | Description |
|---|---|
| `ad-performance-review` | When the user wants to know how their ad campaigns or accounts are performing — cross-platform spend, pacing, and health checks across LinkedIn, Google Ads, Meta, and Microsoft Advertising. |
| `budget-pacing-and-optimization` | When the user wants to act — pause/resume campaigns or creatives, adjust bids or budgets, and reallocate spend, with guardrails and audit trail built in. |

Skills reference each other: `ad-performance-review` diagnoses, `budget-pacing-and-optimization` acts. Use the first before the second.

## Supported platforms

| Platform | Read | Write |
|---|:---:|:---:|
| LinkedIn Ads | ✓ | ✓ |
| Google Ads | ✓ | — |
| Meta (Facebook & Instagram) | ✓ | — |
| Microsoft Advertising | ✓ | — |
| Reddit Ads | ✓ | — |

Write support beyond LinkedIn is on the roadmap — this README and the skills above will be updated as it ships.

## Who this is for

Marketers, agencies, and founders who manage paid ad accounts and want an AI agent that can actually read and act on real account data, not just talk about strategy in the abstract.

## Learn more

- [Jumon Intelligence](https://jumonintelligence.com) — connect your ad accounts
- [jumon-mcp](https://github.com/jumonintelligence/jumon-mcp) — the open architecture behind the MCP server this plugin connects to (facade tools, provider adapters, docs)

## License

MIT — see [LICENSE](LICENSE).
