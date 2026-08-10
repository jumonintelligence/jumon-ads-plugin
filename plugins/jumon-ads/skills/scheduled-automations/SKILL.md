---
name: scheduled-automations
description: "When the user wants to schedule a recurring Jumon report or briefing — asks to 'set up an automation', 'run this every Monday', 'email me a daily CAC check', 'Slack me a weekly pacing summary', 'pause my automation', or 'update the schedule on my report'. Use for creating, listing, editing, enabling/disabling, or deleting scheduled unattended automations. For a one-off performance review right now, use ad-performance-review instead."
metadata:
  version: 1.0.0
---

# Scheduled Automations

You help the user turn a recurring reporting need into a Jumon **scheduled automation**: a saved instruction set plus a schedule that runs unattended and notifies them (private chat transcript, email, optional Slack).

Jumon's tool catalog changes over time. Never assume which automation tools exist from memory — discover them live every session via `explore_platform`.

Tool-calling mechanics (exact parameters, errors, interactive-only rules) live in Jumon's server instructions and each tool's schema. This skill owns **judgment**: when to schedule vs run once, what to ask, and how to write instructions that work without a human in the loop.

## Before creating

1. Call `explore_platform` with no arguments. Note which ad platforms are connected; for disconnected ones, surface `connect_url` if the automation would need them.
2. Discover the Jumon product surface: look for a platform whose tools manage **scheduled automations** (list / get / create / update / delete). Load schemas with `explore_platform` + `tool_names` before calling anything. If no such tools exist, tell the user this isn't supported in their current Jumon MCP build and point them to Jumon → Automations in the dashboard.
3. Clarify (batch into one question when possible):
   - **Cadence** — hourly, daily, weekdays, weekly (which day), or a custom need
   - **Timezone** — never invent one; ask if unclear
   - **Local time** of day (or minute-of-hour for hourly)
   - **Account scope** — whole book of business vs specific accounts
   - **Delivery** — email and/or Slack; if Slack channel id is unknown, prefer email-only and say they can attach Slack in Jumon later
   - **Delivery condition** — always send vs only when something is off (e.g. CAC up, pacing under)
4. Confirm the plan in plain language **before** calling a create/update/delete tool.

## Writing good unattended instructions

Automations run with **no clarifying questions** and **cannot perform live ad writes**. Instructions must:

1. State assumptions explicitly (date window, metrics, thresholds) instead of asking.
2. Prefer portfolio-first analysis when the scope is many accounts; then drill into outliers.
3. Require a verification pass and caveat surfacing for numbers (Jumon reporting is still maturing).
4. Recommend changes in the report — never instruct the run to pause campaigns or change budgets itself.
5. End with a clear send-or-skip intent when the user gave a delivery condition ("only notify when…").

Do not paste the user's one-off chat transcript as-is; rewrite it for an unattended reader.

## Creating

1. Prefer schedule **presets** over free-form cron. Only use custom cron when a preset cannot express the ask.
2. Call create through `execute_write_tool` after loading the schema. Use an allowlisted model id the schema/docs describe; if unsure, pick the default the schema suggests or ask once.
3. After create, always share the response `automationUrl` so the user can open the automation in Jumon. Also read `scheduleSynced` / `scheduleError`. If sync failed, warn that the automation was saved but may not fire until they save again in Jumon.
4. Mention that "run now" stays in the dashboard if they want an immediate test.

## Listing, updating, pausing, deleting

1. List or get first when the user is vague ("my daily report", "the CAC one"). If several match, list candidates and ask which id.
2. Prefer enable/disable toggles for pause/resume when that shape exists, rather than rewriting the whole automation.
3. For full edits, send a complete replace body per the schema — don't invent partial-update fields the schema doesn't support.
4. After any successful update, share `automationUrl` from the response.
5. Confirm before delete. After delete, say the schedule was removed.

## Common mistakes

- Promising the automation will pause ads or change budgets — unattended runs are analysis/recommend only.
- Silent timezone or cadence assumptions.
- Hardcoding tool names or claiming a closed list of platforms.
- Creating Slack delivery without a real channel id.
- Skipping confirmation on create/update/delete.
- Using this skill for a one-off "how's pacing right now" — hand that to `ad-performance-review`.

## Related skills

- `ad-performance-review` — one-off diagnosis and cross-platform summaries.
- `budget-pacing-and-optimization` — interactive write actions after a human is in the loop; not for unattended automation instructions.
