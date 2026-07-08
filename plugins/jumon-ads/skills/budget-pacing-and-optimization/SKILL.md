---
name: budget-pacing-and-optimization
description: "When the user wants to act on ad account performance — pause or resume a campaign or creative, change a bid or budget, reallocate spend, or otherwise optimize a live account based on pacing or performance data. Triggers on 'pause this campaign', 'this is overspending', 'increase the budget', 'adjust bids', 'scale this up', or 'kill this ad'. Use ad-performance-review first if the user hasn't already diagnosed what needs to change."
metadata:
  version: 1.0.0
---

# Budget Pacing and Optimization

You have write access to live ad accounts through Jumon's MCP tools. This skill is about **judgment on when and how to act** — the exact parameters and error codes for each tool live in Jumon's own schemas (`explore_platform`) and are not repeated here.

## Read before you write

Never make a change based on assumption. Before calling a write tool:

1. Pull current state with the matching read tool (e.g. `linkedin_get_budget_pacer_report` or `linkedin_get_campaigns` before touching a LinkedIn campaign). If the user hasn't already run a review, suggest `ad-performance-review` first.
2. Confirm you have the right entity — if the user references "the campaign" or "the account" ambiguously and more than one match exists, list candidates and ask rather than guessing.
3. State what you're about to do and why, in plain language, before calling the write tool. Claude will prompt for confirmation before `execute_write_tool` actually runs — treat that confirmation as a real checkpoint, not a formality.

## What Jumon can currently act on

Write capability today is LinkedIn-only, dispatched through `execute_write_tool`:

| Action | Single-item tool | Batch tool (2–20 campaigns at once) |
|---|---|---|
| Pause / resume campaign | `linkedin_pause_campaign` / `linkedin_resume_campaign` | `linkedin_batch_pause_campaigns` / `linkedin_batch_resume_campaigns` |
| Pause / resume creative | `linkedin_pause_creative` / `linkedin_resume_creative` | — |
| Update bid | `linkedin_update_campaign_bid` | `linkedin_batch_update_campaign_bids` |
| Update budget | `linkedin_update_campaign_budget` | `linkedin_batch_update_campaign_budgets` |

Prefer the batch tool over looping the single-item tool when changing multiple campaigns under one ad account — it's one LinkedIn call instead of many, and each item succeeds or fails independently so one bad campaign never blocks the rest.

Other platforms (Google Ads, Meta, Microsoft Advertising, Reddit) are read-only in Jumon today. Do not imply you can pause or adjust budgets there — tell the user this is a reporting-only connection for now.

## Guardrails you must respect, not just describe

- **Budget changes are capped at roughly ±50% per call by default.** This is a real backend guardrail, not a suggestion — a larger change will be rejected unless the caller explicitly opts out.
- For an intentional larger reallocation, first **ask the user to explain why** in their own words, then pass that reasoning through when bypassing the cap. Do not bypass it just because a tool parameter exists to do so.
- Every write should carry a short, honest explanation of what you're changing and why — this becomes part of the account's audit trail in Jumon. Keep it concise and specific ("pausing due to 3x CPA vs. target over the last 7 days"), not generic ("optimizing performance").

## When a write is rejected

If a write tool comes back with a permission or guardrail error, don't retry it — explain to the user in plain language what happened and what they need to do:

- **Permission-related rejection** — the user's role or their org's settings don't allow this write. Point them to Jumon's dashboard to check write permissions rather than guessing at a workaround.
- **Guardrail rejection** (e.g. the budget change is too large) — explain the size limit conceptually and ask if they want to proceed with an explicit override, requiring their stated reasoning first.
- **Entity-state rejection** (e.g. the campaign is archived) — explain that archived items can't be reactivated and ask what they'd like to do instead.

Never silently retry a rejected write with different parameters hoping it succeeds — a rejected write is a decision point for the user, not a bug to route around.

## After the change

Confirm to the user what changed (before → after), and suggest a reasonable follow-up window to check whether the change had the intended effect — write actions on ad platforms rarely show results instantly.

## Related skills

- **ad-performance-review** — run this first to identify what needs a decision, before reaching for a write tool.
