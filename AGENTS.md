# Jumon — jumon-ads-plugin

Public Claude plugin marketplace for Jumon Intelligence. Markdown skills plus a
reference to the hosted Jumon MCP server. No build, no test suite, no CI.

## Git workflow rules

> **This repo has no `develop` branch.** Docs, README and skill prose may land directly
> on `main`. Anything that changes behaviour — skill logic, a skill `description` (that is
> its trigger surface), `plugin.json`, `marketplace.json`, `.mcp.json` — branches off
> `main` and comes back through a PR.

## Repo map

| Path | Role |
|------|------|
| `.claude-plugin/marketplace.json` | Marketplace manifest (what Claude sees on "Add marketplace") |
| `plugins/jumon-ads/.claude-plugin/plugin.json` | Plugin manifest |
| `plugins/jumon-ads/.mcp.json` | Points at the hosted Jumon MCP (`https://mcp.jumonintelligence.com/mcp`) |
| `plugins/jumon-ads/skills/` | The shipped skills |
| `.cursor/skills/` | Repo-local agent skills (authoring and workflow, not shipped) |

Shipped skills: `ad-performance-review`, `budget-pacing-and-optimization`,
`scheduled-automations`, `linkedin-account-audit`, `linkedin-audience-builder`.

## Key invariants

- The MCP endpoint in `.mcp.json` is **production**. Never point it at localhost in a
  committed file.
- A skill's `description` is its trigger surface. Changing it changes when the skill
  fires, so treat it as behaviour, not copy.
- Skills here run in the user's Claude or Cowork session against **their own** ad
  accounts. Never write a skill that hardcodes an account id, a user id, or a Jumon
  internal endpoint.
- This repo is public. No secrets, no internal URLs, no customer names.

## Skills (invoke with @)

| Skill | When |
|-------|------|
| `ship-feature` | Implementing a Notion ticket scoped to this repo |
| `create-jumon-skill` | Authoring or editing a shipped skill |

## Ticket contract

Work here arrives as a Notion ticket in the **Jumon Tasks** database, designed by Claude
Cowork before the agent is launched. The ticket is the specification.

- Execute the ticket. Do not redesign it, and do not expand beyond its scope line.
- If it is ambiguous, incomplete, or contradicts the repo, set Status to `Blocked`,
  comment saying exactly what is missing, and stop. Do not guess.
- Full workflow: [`.cursor/skills/ship-feature/SKILL.md`](.cursor/skills/ship-feature/SKILL.md).

## Related

- `mcp-ads-manager` — gateway, dashboard, marketing site. Plugin docs live at
  `docs/claude-plugin.md` there.
- `jumon-mcp` — the MCP server this plugin points at.
