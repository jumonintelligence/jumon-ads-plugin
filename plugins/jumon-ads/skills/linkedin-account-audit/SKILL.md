---
name: linkedin-account-audit
description: "When the user wants a LinkedIn Ads account audited or asks what's wrong with their LinkedIn setup: 'audit my LinkedIn account', 'why isn't LinkedIn working', 'check my LinkedIn campaigns for mistakes', 'LinkedIn health check', 'are my settings right', 'review this campaign before launch', or before scaling LinkedIn spend. Checks settings, audiences, bids, forms, offers, formats and measurement against Jumon's LinkedIn playbook. For spend and pacing numbers use ad-performance-review; to apply fixes use budget-pacing-and-optimization."
metadata:
  version: 1.0.0
---

# LinkedIn Account Audit

You are auditing how a LinkedIn Ads account is **set up and judged**, not re-reporting its numbers. Most wasted LinkedIn budget comes from a short list of setup mistakes that are wrong from day one, plus a few reporting habits that make good campaigns look bad and bad ones look good. Find them, rank them by cost, and say exactly what to change.

Read `references/linkedin-playbook.md` before writing findings. It holds the reasoning and benchmarks to cite for each fix.

## Before starting

1. Call `explore_platform` with no arguments. If LinkedIn is not connected, give the user the `connect_url` and stop.
2. Call `explore_platform` with `platform: "linkedin"` to see which tools exist today. Never assume tool names from memory. Batch-load the schemas you need with `tool_names` in one call.
3. Confirm scope: which ad account(s), active campaigns only or paused too (default: active, and say so), and whether this is a **full audit** or a **pre-launch check** of specific campaigns.
4. Ask up to three business questions if you don't already know the answers. They change the verdicts:
   - **Deal size (ACV)** of what they sell. Drives audience size and funnel depth checks.
   - **What they offer** behind the ads (demo, guide, report, trial, incentive).
   - **What counts as success** (form fills, meetings, pipeline, sign-ups) and whether CRM data flows back to LinkedIn.
   If the user doesn't know or says proceed, infer from creatives, forms and conversions, and label it as inferred.

## What to pull

For each in-scope campaign: objective, format, status, start date, cost type, bid strategy and amount, budget, targeting (include AND exclude facets), location type, Audience Expansion flag, LinkedIn Audience Network flag, frequency cap, conversions attached, creatives (format, attached lead form, creative age), lead form questions and hidden fields, and Message Ad copy.

Also look for tools whose summaries cover **audience size** (forecast for a targeting set) and **suggested bid / bid limits** for a campaign. Use them for checks A5, A6 and B1. If no such tool exists for the connected platform, mark those checks Not checked.

Add a closed 30-day performance window (state it) for: spend, impressions, reach, frequency, CTR, landing page clicks, form opens, leads, video completions, and average dwell time. Use it to size findings in dollars and to catch saturation, not to write a performance review.

If a field isn't exposed by any tool, mark that check **Not checked (data unavailable)**, never Passed. After answering, file one `report_platform_feedback` (`limitation`) listing the missing fields.

## The checks

Severity: **High** (actively burning budget), **Medium** (capping results or misleading decisions), **Low** (worth a look).

### A. Settings (fix on day one)

| # | Check | Flag when | Severity |
|---|---|---|---|
| A1 | Audience Expansion | On | High |
| A2 | LinkedIn Audience Network | On for lead gen, conversion or website visit campaigns | High |
| A3 | Location type | "Recent or permanent" instead of permanent only | Medium |
| A4 | Bid strategy | Maximum delivery (automated) on a campaign with meaningful budget | High |
| A5 | Bid level | Manual bid below the suggested bid range, or under-delivering with a bid near the bottom of it | High if under-delivering, else Medium |
| A6 | Bid below the floor | Current bid is below LinkedIn's minimum bid for the campaign's targeting (common after tightening an audience; the campaign can't resume) | High |

### B. Audience

| # | Check | Flag when | Severity |
|---|---|---|---|
| B1 | Audience size | Prospecting audience under ~20k members, or small budget split across many niche audiences instead of one large one | Medium (High if low ACV) |
| B2 | Targeting method | Low-ACV offer targeted by long job title lists instead of broad functions + seniorities with title exclusions | Medium |
| B3 | Company exclusions | No company exclusions at all, or the advertiser's own company or named competitors (ask the user for them) are not excluded | High if none at all, else Medium |
| B4 | Junk segments | Obvious non-buyers not excluded (students, education, tiny companies, irrelevant titles that keep showing up in leads) | Medium |
| B5 | Saturation | Spend rising while reach is flat and frequency climbing (retargeting pools especially); frequency near the cap with no creative refresh | Medium |

### C. Offer and conversion path

| # | Check | Flag when | Severity |
|---|---|---|---|
| C1 | Offer exists | Lead form headline or Message Ad copy offers nothing specific ("Learn more", "Book a demo", a webinar) | High |
| C2 | Incentive tested | No campaign tests an incentive sized to the buyer (credit, gift card, audit) | Medium |
| C3 | Native forms | Lead gen/conversion campaigns send to a landing page when the offer doesn't need proof first | High |
| C4 | Form qualification | Form has no hard qualifying question (company revenue, team size, timeline) | Medium |
| C5 | Form tracking | No hidden fields (UTMs/campaign ids) to attribute leads in the CRM | Medium |
| C6 | Funnel vs ACV | Low ACV but long multi-stage nurture with no direct ask; or high ACV with only a cold demo ask and no retargeting layer | Medium |

### D. Formats and creative

| # | Check | Flag when | Severity |
|---|---|---|---|
| D1 | Free reach unused | No Text or Spotlight ads running on the cold audience or retargeting | Low |
| D2 | Follower ads | Follower ads running outside a 90+ day non-converter pool | Low |
| D3 | Winner isolation | A clear winner (on Thought Leader Ads: 10s+ average dwell time held for a month) is mixed into a test campaign instead of its own always-on campaign | Low |
| D4 | Creative fatigue | Same creatives running for months into a small pool with rising frequency | Medium |
| D5 | Message ad copy | Message ads over ~100 words, product-led, or with no concrete ask | Medium |

### E. Measurement (how results are being judged)

| # | Check | Flag when | Severity |
|---|---|---|---|
| E1 | Wrong benchmark | Campaigns judged on CTR against the wrong format/objective benchmark (see playbook) | Medium |
| E2 | Engagement CTR mistaken for traffic | Thought Leader Ads on engagement reported as if CTR were landing page clicks | Medium |
| E3 | Video views | Video views objective judged on views instead of cost per 50% completion | Low |
| E4 | CRM feedback | No offline/CAPI conversions for later funnel stages (meeting, opportunity, closed won) | Medium |
| E5 | Too-early calls | Campaigns paused or changed within ~2 weeks of launch on thin data | Low |
| E6 | Message ad opens | Open rates compared against desktop data from Jan 2025 to mid-July 2026 (known LinkedIn overstatement) | Low |

**Reading the bid strategy (A4):** LinkedIn doesn't return a "Manual / Maximum delivery / Cost cap" label. Read the campaign's optimization target type: `NONE` or `ENHANCED_CONVERSION` is manual bidding (the bid amount is the advertiser's bid); any `MAX_*` value is maximum delivery (automated); `TARGET_COST_*` is target cost; `CAP_COST_AND_MAXIMIZE_*` is cost cap.

Use judgment, not rules. Brand or hiring campaigns can legitimately skip an offer; enterprise ABM can legitimately use small named-account lists. When you downgrade a check, say why in one line.

## Writing the audit

The user wants to know what to fix first. Keep it scannable.

1. **Headline**: number of High findings and a rough monthly spend exposed to them (sum of 30-day spend on affected campaigns; call it an upper bound, not "wasted").
2. **Fix first**: top three findings. For each: what's wrong, which campaigns (grouped and counted, top offenders named), why it costs money (one line from the playbook), the exact change.
3. **Full checklist**: every check as Pass / Flag / Not checked, one line each, grouped A to E.
4. **Outside this audit**: whether customers, open opportunities and converted leads are excluded (tell the user to confirm their CRM exclusion lists in Campaign Manager), the offer on feed ads, lead quality after the form, post-demo follow-up, and product readiness. If qualified meetings aren't turning into pipeline, say the leak is probably after the demo, not in the ads.

Present the playbook as Jumon's LinkedIn guidance built from real B2B accounts, not as LinkedIn policy or guaranteed results. Remind the user that Jumon's data is still maturing and to confirm settings in Campaign Manager before changing live campaigns.

## Hand-offs

- Don't change anything from this skill. For bids, budgets and pausing, hand off to **budget-pacing-and-optimization**. Settings the tools can't write become a numbered Campaign Manager checklist.
- Targeting edits on an active campaign may require pausing first, and can raise the bid floor. Warn the user before they apply them.
- For spend and pacing, use **ad-performance-review**. To rerun this audit monthly, use **scheduled-automations**.

## Common mistakes to avoid

- Marking a check Passed when the data wasn't available.
- Recommending lower bids to save money. A low bid caps reach without lowering the price paid.
- Recommending a tighter audience for a low-ACV offer. Tighten through exclusions over time, not a smaller inclusion list.
- Calling incentivized or lead form leads low quality by default. Judge on show rate, qualification and close rate.
- Comparing CTR across formats or objectives with one benchmark.
- Blaming the channel when qualified meetings don't close. Check what happens after the demo first.
