---
name: linkedin-audience-builder
description: "When the user wants to build or update LinkedIn saved audiences — 'create an audience for CFOs and VPs of Finance', 'build a target list for this campaign', 'split this into two audiences', 'add these job titles', or 'update our saved audience'. For auditing an existing account's targeting, use linkedin-account-audit; for applying the finished audience to live campaigns, this skill hands off to budget-pacing-and-optimization."
metadata:
  version: 1.0.0
---

# LinkedIn Audience Builder

You are building or updating LinkedIn **saved audiences** (`adTargetTemplate` — reusable include/exclude targeting criteria, not a live campaign). The job that makes this valuable is not clicking through LinkedIn's UI faster — it's getting the **inputs** right before anything is created: real job titles instead of a guess, every facet resolved to a real URN instead of guessed, and a draft the user actually reviews before anything is created or applied.

## Before starting

1. Call `explore_platform` with `platform: "linkedin"`. Never assume tool names or field shapes from memory — this skill was written against a specific tool set and it changes. Load schemas for `linkedin_create_saved_audience`, `linkedin_update_saved_audience`, `linkedin_search_targeting_entities`, `linkedin_get_audience_size`, and `linkedin_apply_saved_audience` (if applying) via `tool_names` before calling any of them.
2. Confirm the ad account and, in one message, batch every open question:
   - What is this audience for (which campaign, or just a reusable template for now)?
   - Does it need to be split into more than one audience (e.g. by seniority or function)? If the user's ask implies a split (different titles for different roles), propose the split back to them rather than building one giant list.
   - Deal size / ACV, if you don't already know it — it decides broad-plus-exclusions vs. a small named list. Use the same judgment as `linkedin-account-audit`'s playbook (`references/linkedin-playbook.md` in that skill): low ACV → broad functions/seniorities with title exclusions; high ACV/ABM → a deliberately built list.

## What a saved audience can and can't hold

Know this before you promise anything:

- It holds **include/exclude targeting facets only** (titles, seniorities, job functions, industries, skills, locations, employers by name, company size, etc.) — resolved as URNs via `linkedin_search_targeting_entities`. Facets cannot be guessed; an unresolved facet is rejected.
- Employer / employersPast / employersAll facets are stripped by LinkedIn's own API and handled through a Jumon overlay merged back in on read/apply — this works, but is worth knowing about if a dry run looks like it dropped an employer exclusion.
- It **cannot hold uploaded email/contact lists or company lists** (LinkedIn matched audiences / DMP segments — Jumon doesn't wrap those). If the user wants "exclude our customer list" or "exclude these named competitors" and that list isn't a small set you can resolve as `employers`/`employersAll` facet entities, say plainly this needs a matched audience built directly in Campaign Manager — don't imply the saved audience covers it.
- It has **no Audience Expansion, LinkedIn Audience Network, or location-type setting** — those live on the *campaign*, not the audience. Don't collect them while building the audience; collect them when applying it (see below).
- Applying a saved audience to a campaign **stamps** today's targeting onto it — it is not a live link. Editing the saved audience later does not update campaigns it was already applied to. Say this to the user; it's a LinkedIn limitation, not a Jumon one.

## Step 0: source the job titles (CRM if connected, otherwise not blocked)

A CRM is a nice-to-have input, not a requirement. Use the best source available, in this order:

1. **CRM connected** (e.g. HubSpot) — pull real job titles from won/pipeline deals before building anything. Scope to open pipeline and closed-won; exclude closed-lost. State what you pulled and how many titles came back.
2. **No CRM, but the account has running or past campaigns** — use `linkedin_get_demographic_engagement` (title/seniority dimension) or `linkedin_get_company_engagement` as a proxy, and say clearly this is inferred from ad engagement, not CRM-confirmed customers.
3. **Neither is available** — ask the user directly for titles, or to paste/attach a list. Do not stall the task waiting on a CRM connection; a user-supplied list is a completely valid input.
4. **User already gave you titles in the request** — skip sourcing, go straight to resolving facets.

Whatever the source, state it plainly in the draft ("from HubSpot closed-won + open pipeline", "inferred from top-engaging titles on your existing campaigns", "as you provided") so the user knows how much to trust the list.

## Resolving facets

1. Call `linkedin_search_targeting_entities` for every raw title/seniority/industry/etc. the user or CRM gave you — never hand a name straight to `targeting_criteria`. Note that `seniorities` and `jobFunctions` (among others) have no keyword search; for those you list the full facet and match by name yourself.
2. Build `targeting_criteria` as `{"include": {"and": [{"or": {facetUrn: [entityUrns]}}]}, "exclude": {"or": {...}}}` only from resolved URNs.
3. Before creating anything, call `linkedin_get_audience_size` with the resolved `targeting_criteria` and surface the estimate (and the under-300 / active-count-null hints if they appear) — this is also when to apply the playbook's audience-size judgment (small budget → one larger audience, not many niche ones).

## Guardrails

- **Never add "CEO" as a job title.** Everyone is a CEO of something on LinkedIn; it makes the inclusion list meaningless. If the user asks for it, say why and suggest company-size or seniority facets instead.
- **Split by real role differences, not just headcount.** e.g. CFO/VP Finance is a different buyer than Controller/Head of AR — keep them as separate audiences if the pitch or creative would differ.
- **Exclude what a facet can actually express.** Employer-name exclusions (a short list of named competitors/own company) work as a facet. A CRM-scale customer/competitor list does not — flag that gap per "What a saved audience can and can't hold" above instead of silently skipping it.

## Always draft before executing

1. Show the user the full resolved title/facet list, grouped by audience, plus the audience-size estimate, before creating anything.
2. Call the write tool with `dry_run: true` first (`linkedin_create_saved_audience`, `linkedin_update_saved_audience`, and `linkedin_apply_saved_audience` all support it) and show the resolved payload — this is the real preview, not just a chat summary.
3. Only after the user confirms, run for real. If creating two or more audiences, use `execute_write_batch` (up to 20 LinkedIn write operations per approval) so the user approves once instead of once per audience — same pattern as `budget-pacing-and-optimization`'s multi-change plans.

## Applying to a campaign (optional, separate step)

If the user wants the audience live now:

1. This is where Audience Expansion, LinkedIn Audience Network, and location type get decided — as campaign settings, not audience settings. Get explicit answers (defaults per the playbook: Expansion off, Audience Network off for lead gen/conversion, permanent location) rather than leaving them on whatever the campaign already has.
2. `linkedin_apply_saved_audience` refuses ACTIVE campaigns unless `apply_to_active=true` with a `why` from the user — prefer cloning the campaign as DRAFT first and applying there, and only override on ACTIVE campaigns when the user explicitly says so.
3. Remind the user this stamps the audience once; it won't follow future edits to the saved audience.
4. For the network-setting toggles themselves, hand off to `budget-pacing-and-optimization` (or use `linkedin_disable_campaign_network_settings` directly if already in this skill's flow) rather than treating them as part of audience creation.

## After creating

Confirm what was created (name, resolved facet/title count, audience-size estimate), and whether it was only saved as a template or also applied to campaigns.

## Common mistakes to avoid

- Blocking on "no CRM connected" instead of falling back to engagement data or asking the user directly.
- Creating the audience before showing the resolved facet list and size estimate for review.
- Treating Audience Expansion / Audience Network / location type as audience-creation settings — they belong to applying, not creating.
- Promising a company/contact-list exclusion that a saved audience can't hold.
- Treating "CEO" as a harmless catch-all title.
- Guessing a facet URN instead of resolving it with `linkedin_search_targeting_entities`.
- Creating audiences one by one with separate approvals when `execute_write_batch` is available.
- Applying to an ACTIVE campaign without an explicit `why` from the user.

## Related skills

- **linkedin-account-audit** — audits an account's *existing* targeting setup; use its playbook for exclusion and ACV-based targeting judgment.
- **budget-pacing-and-optimization** — for applying a finished audience to a live campaign, toggling campaign network settings, or any other write action.
- **scheduled-automations** — not applicable; audience creation needs human review, so it doesn't belong in an unattended run.
