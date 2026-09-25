---
name: linkedin-audience-builder
description: "When the user wants to build or update LinkedIn saved/matched audiences — 'create an audience for CFOs and VPs of Finance', 'build a target list for this campaign', 'split this into two audiences', 'add these job titles', or 'update our saved audience'. For auditing an existing account's targeting, use linkedin-account-audit; to apply the finished audience to a live campaign, use budget-pacing-and-optimization."
metadata:
  version: 1.0.0
---

# LinkedIn Audience Builder

You are building or updating LinkedIn saved/matched audiences. The job that makes this valuable is not clicking through LinkedIn's UI faster — it's getting the **inputs** right before anything is created: real job titles instead of a guess, every audience setting stated instead of left on a default, and a draft the user actually reviews before it goes live.

## Before starting

1. Call `explore_platform` with `platform: "linkedin"` to see which audience tools exist today. Never assume tool names from memory. Batch-load schemas with `tool_names` before calling anything.
2. Confirm the ad account and, in one message, batch every open question:
   - What is this audience for (which campaign or objective)?
   - Does it need to be split into more than one audience (e.g. by seniority or function)? If the user's ask implies a split (different titles for different roles), propose the split back to them rather than building one giant list.
   - Deal size / ACV, if you don't already know it — it decides whether this should be a broad-plus-exclusions audience or a small named list. Use the same judgment as `linkedin-account-audit`'s playbook (`references/linkedin-playbook.md` in that skill): low ACV → broad functions/seniorities with title exclusions; high ACV/ABM → a deliberately built account or title list.

## Step 0: source the job titles (CRM if connected, otherwise not blocked)

A CRM is a nice-to-have input, not a requirement. Figure out what's available and use the best source, in this order:

1. **CRM connected** (e.g. HubSpot) — pull real job titles from won/pipeline deals before building anything. Scope to open pipeline and closed-won; exclude closed-lost. Say what you pulled and how many titles came back before moving on.
2. **No CRM, but the account has running or past campaigns** — look at job-title/seniority engagement data on existing campaigns (converted leads or top-engaging segments) as a proxy, and say clearly that this is inferred from ad engagement, not CRM-confirmed customers.
3. **Neither is available or connected** — ask the user directly for the titles, or to paste/attach a list (a spreadsheet of customers, a manually typed list). Do not stall the task waiting on a CRM connection; a user-supplied list is a completely valid input.
4. **User already gave you titles in the request** — skip sourcing, go straight to the draft.

Whatever the source, state it plainly in the draft ("from HubSpot closed-won + open pipeline", "inferred from top-engaging titles on your existing campaigns", "as you provided") so the user knows how much to trust the list.

## Guardrails (apply regardless of source)

- **Never add "CEO" as a job title.** Everyone is a CEO of something on LinkedIn; it makes the inclusion list meaningless. If the user asks for it, say why and suggest company-size or seniority facets instead.
- **Split by real role differences, not just headcount.** e.g. CFO/VP Finance is a different buyer than Controller/Head of AR — keep them as separate audiences if the pitch or creative would differ.
- **Exclude before you include.** Carry over standing exclusions from the account (current customers, competitors, own employees, people who already converted) into every new audience — check `linkedin-account-audit`'s playbook exclusion list (section 4) if you don't already have the account's standing exclusions.

## Always draft before executing

1. Show the user the full title list (or facet list) grouped by audience, before creating anything. This is non-negotiable — it's the single biggest trust issue with unreviewed audience builds.
2. For every audience, get an explicit answer on each setting instead of leaving it on a platform default:
   - Audience Expansion (default recommendation: off — see playbook A1)
   - Location type: recent-or-permanent vs. permanent only (default recommendation: permanent — playbook A3)
   - LinkedIn Audience Network (default recommendation: off for lead gen/conversion — playbook A2)
   - Save as draft or permanent, and the audience name
   - Any exclusions beyond the account's standing list
   Do not let any of these stay unspecified — an unset setting is exactly what causes rework later.
3. Only after the user confirms the draft, create the audiences. If two or more audiences are being created, use `execute_write_batch` so the user approves once instead of once per audience — same pattern as `budget-pacing-and-optimization`'s multi-change plans.

## After creating

Confirm what was created (name, title/facet count, settings applied), and remind the user this is a saved audience, not a live campaign — attaching it to a campaign is a separate step (hand off to `budget-pacing-and-optimization` if they want to apply it now).

## Common mistakes to avoid

- Blocking on "no CRM connected" instead of falling back to engagement data or asking the user directly.
- Creating the audience before showing the title list for review.
- Leaving Audience Expansion, location type, or Audience Network unspecified and letting the platform default decide.
- Treating "CEO" as a harmless catch-all title.
- Bundling clearly different buyer roles into one audience because it's faster.
- Creating audiences one by one with separate approvals when `execute_write_batch` is available.

## Related skills

- **linkedin-account-audit** — audits an account's *existing* targeting setup; use its playbook for exclusion and ACV-based targeting judgment.
- **budget-pacing-and-optimization** — for applying a finished audience to a live campaign, or any other write action.
- **scheduled-automations** — not applicable; audience creation needs human review, so it doesn't belong in an unattended run.
