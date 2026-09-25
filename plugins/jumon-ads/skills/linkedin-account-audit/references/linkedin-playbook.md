# Jumon LinkedIn Ads Playbook

The reasoning behind each audit check, built from real B2B LinkedIn accounts. Use it to explain *why* a fix matters. Figures are typical ranges seen in practice, not guarantees. Add new sections as knowledge comes in; one idea per bullet.

## The operating standard

Native forms, a real offer (often incentivized), manual bids, exclusions stacked from day one, defaults turned off. Then stop guessing and let account data decide what to scale.

Pre-launch checklist for every campaign: naming convention, objective, format, audience, budget, Audience Expansion OFF, LinkedIn Audience Network OFF, permanent location, manual bid, correct start date, form with hidden tracking fields.

## 1. Settings

- LinkedIn's defaults are built for LinkedIn's revenue, not the advertiser's.
- **Audience Expansion** pushes ads outside the audience you defined. Turn it off.
- **LinkedIn Audience Network** serves ads on third-party apps and sites. Low-quality traffic for B2B lead gen. Turn it off.
- **Location**: use permanent location. "Recent or permanent" includes travelers and people passing through.
- **Desktop-only placement** sounds smart but can shrink a 30k to 50k audience to about 3k. That's a retargeting audience: CPM spikes and the campaign never leaves learning. No reliable evidence desktop out-converts mobile.

## 2. Bidding

- LinkedIn runs a second-price style auction. Bid above the suggested range and you still pay roughly what the market clears at. Example: an $18 manual bid paying under $4.
- Bidding low doesn't save money. It caps reach.
- **Maximum delivery** (automated) quietly overspends. Prefer manual bids.
- **Cost cap** can make sense in a narrow case: a reach/impressions campaign with a small daily budget where max delivery pushes CPM far too high.
- **Bid floors move.** Tightening targeting can raise LinkedIn's minimum bid above the current bid. The campaign then refuses to resume until the bid is raised.
- Targeting edits on active campaigns can fail; pausing first is often required.

## 3. Audience size and targeting

- About 95% of a B2B market is not buying right now. A 20,000-person audience holds roughly 1,000 in-market buyers; once swept, the pool is dry. At 100,000 it's about 5,000.
- Bigger is also cheaper. Message ads run roughly $0.40 to $0.60 per send on a wide audience (around $1 to reach deep into it). Squeezing a 20k pool pushes toward $2 per send for the same people.
- With a modest budget, run one large audience (around 50k) for a long time rather than splitting into many niche segments.
- **Low ACV: go broad, then exclude.** Target functions and seniorities, then exclude job titles. LinkedIn won't mix job titles with functions/seniorities in inclusions, but allows around 100 title exclusions. Ten to thirty titles usually eat most impressions. Cut them weekly against what actually closed in the CRM. Expect to find the real audience in about two months.
- Tightening a low-ACV audience from the inclusion side kills volume. Example: CPL went from about $7 to $150 on a $700 product, which made the math unworkable.
- For enterprise/ABM, build the company list deliberately (a named TAM) instead of relying on lookalikes or predictive audiences, which can't be inspected or explained.
- LinkedIn's taxonomy slips: job titles are self-entered and companies sometimes pick the industry they sell to. Expect some leakage and keep excluding what shows up in leads (students, higher education, companies under the size floor, irrelevant titles).
- **Segment by exposure**: exclude companies that have already seen the ads heavily; push under-exposed ICP accounts. Penetration before frequency.

## 4. Exclusions

Every impression on these people is wasted. Exclude from day one:
- Current customers
- Open opportunities
- Competitors
- Own employees
- People who already converted
- Accounts actively worked by outbound, or coordinate so they aren't hit from every side at once

Sync these from the CRM as matched audiences so they stay current.

## 5. Offers

- "Learn more" is not an offer. "Book a demo" is not an offer; nobody wakes up wanting a demo. A webinar usually isn't either.
- Real offers: a benchmark report, checklist, calculator, case study with the result on the cover, free trial, free audit.
- Example from one account, same audience, same month, no incentive: webinar behind a form cost about $2,861 per lead at 1.7% form completion; a real document behind the same form cost about $405 per lead at 50% completion.
- Gate mid and bottom funnel only. Show two or three preview pages before asking.

## 6. Incentives

- Incentivized offers (gift cards, product credits, free audits) consistently lower cost per lead and raise completion. LinkedIn's own study (6 customers, 233 lead gen campaigns): incentivized forms completed 34% more often at 62% lower cost per lead. One customer that switched incentives off went from $604 to $2,657 per lead.
- In practice, incentivized leads also close better; the "incentivized leads are junk" belief doesn't hold up.
- **Size the incentive to the buyer, not the deal.** Examples: a $5 credit for practitioners, about $95 per lead; gift card to mid-market, about $311; gift card to a broad enterprise list, about $459.
- Tailored gifts can win, but test many and cut fast. Gift cards and AirPods remain the reliable fallback.
- Incentivized demo meetings commonly land around $1k to $2k per qualified meeting.
- Always offer an escape hatch: if gifts aren't allowed at their company, the same value goes to a charity they pick.

## 7. Native lead forms

- Send traffic to Lead Gen Forms, not landing pages. Forms prefill from the profile. Industry data: forms convert around 15 to 20%, landing pages 4 to 9%. Fixing this alone often pays for every other fix.
- Form leads are exactly as good as the questions. Add one or two hard qualifying questions (e.g. an annual revenue dropdown) so the wrong people quit before submitting.
- Winning pattern: wider audience, harder form. The form filters what targeting can't.
- Always add hidden fields (UTMs, campaign id) so leads attribute correctly in the CRM.
- Landing pages still win when the offer needs proof before the ask.

## 8. Funnel depth vs ACV

- Around $5k a year ACV doesn't need a six-touch nurture. Ask for the demo.
- High ACV: CEO/leader Thought Leader Ads into a retargeting pool, then a demo or pilot ask as the capture step; a guide or research piece works as mid-funnel.
- New accounts: launch, wait about two weeks before judging results, confirm conversion tracking and follow-up sequences, then test direct demo asks (e.g. message ads) around week four.

## 9. Formats

**Thought Leader Ads (TLA)**
- Posted from real people's profiles; feels peer-to-peer and outperforms brand-page ads with senior audiences. Match the speaker's seniority to the audience.
- Third-party creator posts can outperform internal ones.
- Engagement objective CTR counts every click (likes, "see more", profile taps). It is not landing page clicks. A 10% CTR TLA can send fewer site visits than a 6% one.
- **Dwell time** is the better quality signal: 10+ seconds, held for a full month, earns a permanent always-on slot. Buyers often read, never click, and show up months later.
- Video views objective is now available for TLAs. A LinkedIn "view" is 2 seconds at 50% on screen; report cost per 50% completion instead.

**Message ads**
- Billed per send: every wrong recipient is pure waste. Tight fit matters more here than anywhere.
- Under 100 words. One line of pain they'd say out loud this week; the offer or gift and why it's unusual; an ask sized in minutes ("20 minutes watching it do your job", not "a demo"); the escape hatch; link; first name.
- No paragraphs about the product. Don't open with industry trend statements.
- Desktop open rates from January 2025 to mid-July 2026 were overstated by LinkedIn (opens counted when the member merely visited LinkedIn on desktop). Historical data was not corrected. Don't benchmark against it.

**Text and Spotlight ads**
- Right rail, desktop only, charged per click, and rarely clicked. Often 10k to 100k impressions for close to $0.
- Run one on the cold audience and one on retargeting, 5 to 10 variations each, for a month or more; frequency spikes fast.

**Follower ads**
- Usually poor cost per follower. Only worth it for people who engaged or visited 90+ days ago and haven't acted.

**Winners**
- Find the winner, isolate it in its own always-on campaign, and leave it running. Don't over-optimize weekly.
- Rough split: proven winners about 40% of budget, new tests about 60%.

## 10. CTR benchmarks by format

Never judge every campaign against one average.

| Format | Good | Winner | Problem |
|---|---|---|---|
| Thought Leader Ads (engagement) | 4 to 8% | 10%+ | under 3% |
| Single image (traffic) | 0.4 to 0.8% | 1%+ | well under 0.4% |
| Video | CTR barely matters; 0.3 to 1% engagement rate | strong watch time | |
| Text ads | near 0% is normal | judge on impressions and companies reached | |

## 11. Saturation and frequency

- Warning pattern: spend rising, reach flat, frequency climbing. That's money poured into a saturated pool, common in retargeting.
- Refresh creatives before the pool fatigues; keep a frequency cap (around 10 is a common ceiling).

## 12. Measurement and attribution

- Last click almost never credits LinkedIn. Buyers consume content for months, then search the brand and arrive as direct or paid search.
- Add a self-reported "How did you hear about us?" field. Read it and weight it.
- Send CRM stages back to LinkedIn via CAPI/offline conversions (lead, meeting, opportunity, closed won) with values, so LinkedIn optimizes on pipeline, not just form fills.
- HubSpot to LinkedIn sync: choose the option that sends **every contact**, not only those HubSpot thinks touched an ad. Otherwise view-through matches are lost before LinkedIn sees them. Share every identifier available.
- Influenced pipeline: only count deals with real ad exposure inside a defined window (e.g. 90 days) using tiered thresholds. Don't count a single impression.
- Company engagement data can feed SDR call lists and outbound targeting.

## 13. When the ads aren't the problem

- If form fills show up to calls and qualify, but nothing closes, the leak is after the demo: no follow-up content, no retargeting during the committee phase. Audit the 90 days after the demo before cutting the channel.
- If the product or onboarding isn't ready, pause spend. Media buying can't outrun product readiness.
