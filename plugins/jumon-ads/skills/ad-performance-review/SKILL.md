---
name: ad-performance-review
description: "When the user wants to know how their ad campaigns or accounts are performing — asks things like 'how are my campaigns doing', 'give me a performance review', 'how's my ad spend', 'how are we pacing this month', or wants a cross-platform summary across their connected ad platforms (e.g. LinkedIn, Google Ads, Meta, Microsoft Advertising). Also use for 'spend report', 'ROAS check', or 'campaign health check'. For pausing, resuming, or adjusting budgets/bids based on the review, use budget-pacing-and-optimization after this skill's diagnosis."
metadata:
  version: 1.3.0
---

# Ad Performance Review

You are reviewing live ad account performance through Jumon's MCP tools. Your job is to turn a vague "how are things going" into a structured, cross-platform answer grounded in real data — not to restate tool mechanics, which live in Jumon's own tool schemas and server instructions.

Jumon's platform and tool catalog changes over time — new platforms and tools are added without this skill being updated. Never assume what's available from memory; always discover it live.

## Before starting

1. Call `explore_platform` with no arguments to see which platforms are connected and usable right now. Only review platforms that are connected — for disconnected ones, surface the `connect_url` from the response and move on.
2. If the user named a specific platform or account, skip straight to it. If they asked generally ("how's everything doing"), review every connected platform returned by `explore_platform` — don't limit yourself to platforms you happen to recall from past sessions.
3. Confirm the time window before pulling data. If the user did not give one, state the range you will assume (this mirrors Jumon's own disambiguation protocol — do not silently default). Prefer a **closed multi-day** window over today/yesterday for cross-platform reviews. Pass the **same calendar dates** to every platform (**Option A**) — do not shift dates per platform to fake a shared UTC wall-clock window. Same calendar labels are **not** the same wall-clock windows: LinkedIn/Reddit use UTC days; Google/Meta use each account's timezone; Microsoft uses `ReportTimeZone`. Cross-platform totals are approximate.

## Finding the right tool on each platform

Do not assume you already know the exact tool names or that today's set is complete. For each connected platform:

1. Call `explore_platform` with that platform to list its available tools and summaries.
2. Look for a tool whose summary describes spend, pacing, or performance reporting — that's usually the fastest path to answering "how's it going." If none stands out, a general campaign/account listing tool plus a reporting tool is the fallback.
3. Load the exact schema for the tool(s) you picked via `explore_platform` with `tool_names` before calling them — never guess parameters.
4. If a platform has a dedicated pacing tool (spend vs. expected/budget), prefer it over raw analytics for a "how's it going" question; save deeper analytics tools for when the user wants detail beyond simple pacing (demographics, conversions, drill-down by ad/ad set, etc.).

For "give me everything" or "all conversions" style asks, pull every relevant field/category rather than one representative metric — Jumon's tools will tell you if a response was truncated or split; follow that guidance rather than guessing.

## Synthesizing a cross-platform review

A good review is not one dump per platform. Structure the answer as:

1. **Headline** — total spend across connected platforms for the period, and whether it's broadly on pace, over, or under.
2. **Per-platform pacing** — for each platform, spend vs. expected/budget, called out as on-pace / over-pacing / under-pacing. Use whatever pacing signal that platform's tools provide directly; where no dedicated pacing tool exists, compare period-to-date spend against the account's budget or a prior comparable period.
3. **What's driving it** — the campaigns or ad sets responsible for the biggest spend or the biggest pacing deviation, not every row of data.
4. **What needs a decision** — flag anything that looks like it needs a pause, budget change, or investigation, but do not take action in this skill. Hand off to `budget-pacing-and-optimization` if the user wants to act on it.
5. **Timezone footnote (required for cross-platform)** — end with a short data-source note listing each platform's reporting timezone (from `assumed_date_range.time_zone`, account metadata, or Microsoft `report_time_zone`). State that cross-platform totals are approximate and were **not** normalized to UTC.

If a tool response includes `assumed_date_range` (including `time_zone`), `metadata.truncated`, or a `hint`, surface that to the user in plain language — these are Jumon signaling that it made an assumption or hit a limit, and hiding that erodes trust in the numbers.

When you present the review headline or any spend/conversion totals, briefly note that Jumon MCP reporting is still maturing / experimental and the user should double-check critical numbers in each platform's native ads UI before acting or sharing with clients.

## Common mistakes to avoid

- Don't present MCP figures as final truth without the double-check reminder above — especially for client reports, budget changes, or pauses.
- Don't compare raw spend numbers across platforms without noting different attribution windows, reporting lag, **and reporting timezones** (Option A) — never claim day totals were UTC-aligned.
- Don't present a single summed "yesterday" across platforms as if it were one consistent 24-hour window.
- Don't pick a single conversion metric when the user asked for "all conversions" — pull the full category and let the data show what matters.
- Don't silently assume a comparison period ("vs last month") — ask if it's ambiguous between calendar month and trailing 30 days.
- Don't try to execute optimizations from this skill — diagnose here, act in `budget-pacing-and-optimization`.
- Don't assume a platform is unsupported or a tool doesn't exist because it wasn't true in a past session — always check `explore_platform` fresh.

## Related skills

- **budget-pacing-and-optimization** — for pausing, resuming, or adjusting budgets/bids based on what this review surfaces.
