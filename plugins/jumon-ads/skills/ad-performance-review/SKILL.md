---
name: ad-performance-review
description: "When the user wants to know how their ad campaigns or accounts are performing — asks things like 'how are my campaigns doing', 'give me a performance review', 'how's my ad spend', 'how are we pacing this month', or wants a cross-platform summary across LinkedIn, Google Ads, Meta, or Microsoft Advertising. Also use for 'spend report', 'ROAS check', or 'campaign health check'. For pausing, resuming, or adjusting budgets/bids based on the review, use budget-pacing-and-optimization after this skill's diagnosis."
metadata:
  version: 1.0.0
---

# Ad Performance Review

You are reviewing live ad account performance through Jumon's MCP tools. Your job is to turn a vague "how are things going" into a structured, cross-platform answer grounded in real data — not to restate tool mechanics, which live in Jumon's own tool schemas and server instructions.

## Before starting

1. Call `explore_platform` with no arguments to see which platforms are connected and usable. Only review platforms that are connected — for disconnected ones, surface the `connect_url` from the response and move on.
2. If the user named a specific platform or account, skip straight to it. If they asked generally ("how's everything doing"), review every connected platform.
3. Confirm the time window before pulling data. If the user did not give one, state the range you will assume (this mirrors Jumon's own disambiguation protocol — do not silently default). Use the same window across all platforms so the cross-platform comparison is apples-to-apples.

## Routing by platform

Do not restate exact parameters here — call `explore_platform` with `tool_names` to load the schema for the tool you need. This table only tells you **which tool answers which question**.

| Platform | Primary tool for spend/pacing | Use instead for structure or drill-down |
|---|---|---|
| LinkedIn | `linkedin_get_budget_pacer_report` — spend vs. expected, by campaign and campaign group | `linkedin_get_campaign_groups` / `linkedin_get_campaigns` for raw structure; `linkedin_get_ad_analytics` for demographics, conversions, CRM revenue |
| Google Ads | Curated report tools (start with `google_list_ad_accounts` or `google_resolve_customer` to find the account) | `google_search_keywords`, `google_search_search_terms`, `google_search_pmax_search_terms` for channel-specific detail |
| Meta | `meta_search_ad_entities` with `level: adset` or `level: ad` for performance | `meta_list_campaigns` → `meta_list_ad_sets` → `meta_list_ads` for structure; `meta_get_delivery_errors` if something isn't spending |
| Microsoft Advertising | `microsoft_get_performance_report` | `microsoft_list_campaigns` → `microsoft_list_ad_groups` for structure |

For "give me everything" or "all conversions" style asks, pull every relevant field/category rather than one representative metric — Jumon's tools will tell you if a response was truncated or split; follow that guidance rather than guessing.

## Synthesizing a cross-platform review

A good review is not five separate dumps. Structure the answer as:

1. **Headline** — total spend across connected platforms for the period, and whether it's broadly on pace, over, or under.
2. **Per-platform pacing** — for each platform, spend vs. expected/budget, called out as on-pace / over-pacing / under-pacing. Use whatever pacing signal the platform's tool provides (LinkedIn's pacer gives this directly; for Meta/Google/Microsoft, compare period-to-date spend against the account's budget or a prior comparable period).
3. **What's driving it** — the campaigns or ad sets responsible for the biggest spend or the biggest pacing deviation, not every row of data.
4. **What needs a decision** — flag anything that looks like it needs a pause, budget change, or investigation, but do not take action in this skill. Hand off to `budget-pacing-and-optimization` if the user wants to act on it.

If a tool response includes `assumed_date_range`, `metadata.truncated`, or a `hint`, surface that to the user in plain language — these are Jumon signaling that it made an assumption or hit a limit, and hiding that erodes trust in the numbers.

## Common mistakes to avoid

- Don't compare raw spend numbers across platforms without noting different attribution windows or reporting lag (Meta/Google attribution can differ meaningfully from LinkedIn's).
- Don't pick a single conversion metric when the user asked for "all conversions" — pull the full category and let the data show what matters.
- Don't silently assume a comparison period ("vs last month") — ask if it's ambiguous between calendar month and trailing 30 days.
- Don't try to execute optimizations from this skill — diagnose here, act in `budget-pacing-and-optimization`.

## Related skills

- **budget-pacing-and-optimization** — for pausing, resuming, or adjusting budgets/bids based on what this review surfaces.
