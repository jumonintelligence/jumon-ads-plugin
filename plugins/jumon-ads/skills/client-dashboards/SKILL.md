---
name: client-dashboards
description: "When the user wants a client dashboard, white-label dashboard, or shareable client-facing report — asks to 'build a client dashboard', 'create a white-label dashboard', 'share a dashboard with my client', 'build a report for my client', or 'clone this dashboard for another client'. Use for publishing a recurring client-facing page, not for a one-off performance diagnosis in chat (use ad-performance-review for that)."
metadata:
  version: 1.0.0
---

# Client Dashboards

You help the user build a **client dashboard**: a shareable, white-label page an agency's client will open again — not a one-off answer in chat.

Jumon's tool catalog changes over time. The dashboard tools below may not be present in every MCP build yet. Discover them live every session; if a named tool is missing, say so plainly and do not invent a substitute.

Tool parameter schemas live in Jumon's tool definitions. This skill owns **judgment**: when a dashboard is the right deliverable, which blocks belong above the fold, how slots work, and the honesty rules a client-facing page demands.

## Dashboard tools

These are the dashboard surface — load each tool's schema before calling it; never restate parameters from memory:

- `jumon_create_dashboard`
- `jumon_update_dashboard`
- `jumon_preview_dashboard_block`
- `jumon_duplicate_dashboard`
- `jumon_share_dashboard`
- `jumon_list_dashboards`
- `jumon_get_dashboard`

If `explore_platform` (or the live tool list) does not expose these yet, tell the user client dashboards are not available in their current Jumon MCP build and stop — do not call a missing tool or fake a page.

## When to build a dashboard

Reach for a dashboard when the user wants a **recurring client-facing view** someone will open again: a white-label page, a shareable client report, or a template to clone per client.

Stay in chat (hand off to `ad-performance-review`) when the ask is a one-off diagnosis — "how's pacing this week", "why is CAC up", "give me a quick spend check". Those are answers, not publishable pages.

## Before creating

1. Confirm the audience and purpose: which client, what they should see first, and whether this is a new dashboard or a clone of an existing one.
2. Confirm account scope. A dashboard binds a named **slot** (account set). Building one for a client and **cloning it for the next** (`jumon_duplicate_dashboard`, then rebind the slot) is the intended workflow — not rebuilding each from scratch.
3. Discover the seven tools above. Load schemas. If any required tool is missing, stop and say so.
4. Confirm the plan in plain language before create/update/share/delete.

## Choosing blocks

Start above the fold with:

1. **Spend**
2. **Conversions**
3. **Cost per conversion**

Put the **campaign table** below the fold — detail, not the first impression.

Avoid these block types in dashboards; they are too slow for a client-facing page (measured p95 latency over 60 days of gateway traffic: `google_search_search_terms` ~23.6s, `linkedin_search_creatives` ~15.3s). Prefer faster summary and campaign-level blocks instead.

Use `jumon_preview_dashboard_block` when you need to validate a block before committing it to the page.

## Honesty rules (non-negotiable on a client-facing page)

1. A missing metric renders **"not measured"** — never zero. Zero means measured and empty; missing means unknown.
2. **Cross-platform totals are approximate** because each platform closes its day in its own timezone.
3. **Conversion counts come from the ad platform's attribution** and will not match a GA4 or CRM figure. Say so when conversions appear on the page.

### Known gap: no analytics or CRM connector

Jumon has no analytics or CRM connector, so blended cost per GA4 key event (or any CRM-sourced conversion) **cannot** be produced. Say so plainly. Do not approximate it from ad-platform conversions, invent a blended number, or quietly omit the limitation.

## Publishing and sharing

1. Create or update the dashboard, then call `jumon_share_dashboard` (or the share path the schema exposes).
2. Hand the agency the **share URL**. For external MCP clients that is the whole deliverable in v1 — no separate marketing-site or portal step.
3. When the next client needs the same layout, duplicate and rebind the slot rather than starting over.

## Common mistakes

- Answering a one-off performance question by publishing a dashboard — diagnose in chat with `ad-performance-review` instead.
- Building every client dashboard from scratch instead of duplicating and rebinding the slot.
- Putting search-term or creative-search blocks on a client page (`google_search_search_terms`, `linkedin_search_creatives`).
- Showing missing metrics as `0`.
- Presenting cross-platform day totals as exact, or treating ad-platform conversions as GA4/CRM truth.
- Inventing a GA4 or CRM blended metric Jumon cannot produce.
- Calling dashboard tools that are not in the live catalog, or restating parameter schemas from this skill instead of loading them.

## Related skills

- `ad-performance-review` — one-off diagnosis and cross-platform summaries in chat; not for publishing a client page.
- `scheduled-automations` — recurring unattended briefings delivered to the agency; complementary to a shareable client dashboard, not a substitute.
