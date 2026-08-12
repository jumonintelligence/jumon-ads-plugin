---
name: budget-pacing-and-optimization
description: "When the user wants to act on ad account performance — pause or resume a campaign or creative, change a bid or budget, reallocate spend, or otherwise optimize a live account based on pacing or performance data. Triggers on 'pause this campaign', 'this is overspending', 'increase the budget', 'adjust bids', 'scale this up', or 'kill this ad'. Use ad-performance-review first if the user hasn't already diagnosed what needs to change."
metadata:
  version: 1.3.1
---

# Budget Pacing and Optimization

You have write access to live ad accounts through Jumon's MCP tools. This skill is about **judgment on when and how to act** — the exact parameters, error codes, and which platforms/tools support writes today all live behind `explore_platform` and change over time. Never assume write capability from memory; confirm it live every time.

## Read before you write

Never make a change based on assumption. Before calling a write tool:

1. Pull current state with a matching read tool for that platform (found via `explore_platform`) before touching any entity. If the user hasn't already run a review, suggest `ad-performance-review` first.
   - When the decision spans many accounts ("which clients are overspending, fix the worst ones"), start from the platform's **portfolio** read tool — one call covering many accounts — rather than reading each account in turn. Then act only on the accounts that call for it.
   - Do not act on an account the portfolio response listed under `skipped` or `pending_account_ids`: it was not measured, so you have no basis for the change. Read that account directly first.
2. Confirm you have the right entity — if the user references "the campaign" or "the account" ambiguously and more than one match exists, list candidates and ask rather than guessing.
3. State what you're about to do and why, in plain language, before calling the write tool. Claude will prompt for confirmation before `execute_write_tool` actually runs — treat that confirmation as a real checkpoint, not a formality.

## Pacing signals before pause / budget changes

When the user wants to act because something is "overspending" or "underspending", re-read the latest pacing/performance fields for that entity (not just the label):

1. **Delivery eligibility first.** If the entity is not delivery-eligible (paused, billing hold, outside flight, etc.), fix or explain status/serving — do not pause again or cut budget as if it were an auction problem.
2. **Soft daily vs hard lifetime.** Soft daily over-delivery is often normal; prefer investigating or a modest budget tweak over an automatic pause. Lifetime ceilings and approaching-lifetime-cap signals are harder stops — surface them before scaling spend up.
3. **Trend shape.** Still ramping + under → wait or small increase, don't pause. Capped while eligible → audience/creative/frequency path. Stopped / not eligible → status path. Declining with recent daily spend well below the required rate → investigate delivery before scaling.
4. **Honor tool `hint` fields** when present — they encode the above judgment without you inventing thresholds.
5. Never invent a projected period total from a run rate to justify a write; use required vs recent daily spend (or the tool's own guidance) instead.

## Confirm write capability before promising it

Do not assume which platforms or actions currently support writes — this changes as Jumon adds capability. Before proposing a write action:

1. Call `explore_platform` for that platform and look for a tool whose action type and summary indicate it performs the mutation the user wants (pause, resume, bid change, budget change, etc.).
2. If a matching write tool exists, load its schema via `explore_platform` with `tool_names` before calling it.
3. If no matching write tool exists for that platform, tell the user plainly that this is reporting-only for that platform today — don't imply a capability that isn't there, and don't assume it will never exist.
4. If you need to change several similar entities under one account, check whether a batch-style write tool exists for that action before calling the single-item tool in a loop — batch tools (when available) are more efficient and let each item succeed or fail independently.

## Guardrails you must respect, not just describe

- Write tools may enforce server-side guardrails (e.g. capping how large a single budget or bid change can be per call). Treat a guardrail rejection as a real constraint, not a bug — don't retry with slightly different parameters hoping it slips through.
- If a tool's response or schema exposes an explicit override parameter for bypassing a guardrail, only use it after the user has explicitly explained why the larger change is intentional, and carry that reasoning through in the call.
- Every write should carry a short, honest explanation of what you're changing and why — this becomes part of the account's audit trail in Jumon, when the tool supports one. Keep it concise and specific ("pausing due to 3x CPA vs. target over the last 7 days"), not generic ("optimizing performance").

## When a write is rejected

If a write tool comes back with a permission or guardrail error, don't retry it — explain to the user in plain language what happened and what they need to do, using whatever explanation the tool's error response provides:

- **Permission-related rejection** — the user's role or their org's settings don't allow this write. Point them to Jumon's dashboard to check write permissions rather than guessing at a workaround.
- **Guardrail rejection** (e.g. the change is too large) — explain the constraint conceptually using the tool's own error message, and ask if they want to proceed with an explicit override, requiring their stated reasoning first.
- **Entity-state rejection** (e.g. the item is archived or otherwise locked) — explain what that state means and ask what they'd like to do instead.

Never silently retry a rejected write with different parameters hoping it succeeds — a rejected write is a decision point for the user, not a bug to route around.

## After the change

Confirm to the user what changed (before → after), and suggest a reasonable follow-up window to check whether the change had the intended effect — write actions on ad platforms rarely show results instantly.

## Related skills

- **ad-performance-review** — run this first to identify what needs a decision, before reaching for a write tool.
- **scheduled-automations** — for recurring unattended reports; do not put live write actions into automation instructions.
