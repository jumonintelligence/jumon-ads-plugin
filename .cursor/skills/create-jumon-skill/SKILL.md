---
name: create-jumon-skill
description: Author or revise a Claude plugin skill in plugins/jumon-ads/skills/ for the jumon-ads plugin. Use when adding a new SKILL.md, splitting an existing one, or reviewing a skill for staleness risk.
disable-model-invocation: true
---

# Create a Jumon plugin skill

Skills here teach Claude *marketing judgment* on top of Jumon's MCP tools — not tool mechanics (those live in jumon-mcp's `server_instructions.md` and each tool's own schema). See [docs/claude-plugin.md](https://github.com/jumonintelligence/jumon-mcp/blob/main/docs/claude-plugin.md) in jumon-mcp for the full instructions-vs-skills split.

## The core rule: write for a growing, unknown tool catalog

Jumon adds platforms and tools over time, without ever touching this repo. A skill must stay fully useful after every such addition, with zero edits. Concretely:

**Never hardcode:**
- Specific provider tool names (anything not one of the four facade tools)
- A closed list of supported platforms in the skill *body* (frontmatter `description` may still name a few platforms for discovery/triggering — that's about when Claude should invoke the skill, not a factual capability claim)
- Capability claims like "X is read-only today" or "only platform Y supports this" — these become false, and tell the agent to lie, the moment Jumon ships more
- Numeric guardrails, limits, or thresholds that are enforced server-side (e.g. a max change size) — these are backend policy values that can change independently of this repo

**Always instruct the agent to discover instead:**
- "Call `explore_platform` to see what's connected and what tools exist for it" — every time, never from memory or a past session
- "Look for a tool whose summary/purpose matches X" rather than naming the tool
- "If no matching tool exists, tell the user this isn't supported *today* — don't claim it never will be"
- "Guardrails are enforced server-side; surface the rejection reason from the tool's response rather than asserting a number"

**The four facade tools are exempt** (they're the stable, protocol-level MCP surface, not provider tooling): `explore_platform`, `execute_read_tool`, `execute_write_tool`, `report_platform_feedback`.

## Litmus test

Before finalizing, delete every provider-tool name and platform-specific capability claim from the draft. If the skill is still fully actionable — the agent still knows *what to check, in what order, and how to decide* — it passes. If deleting those lines leaves a gap, the skill was relying on a snapshot instead of a process; rewrite that section as a discovery step.

## Structure to follow

Match the existing skills' shape:

1. Frontmatter: `name`, `description` (third person, states WHAT + WHEN, may name a few platforms/verbs for triggering), `metadata.version` (semver; bump minor for judgment/process changes, not for keeping up with new tools/platforms since the skill shouldn't need those edits at all).
2. A short framing paragraph naming what this skill is for and explicitly noting the tool catalog changes over time — never assume from memory.
3. Sequenced guidance sections (e.g. "Before starting", "Finding the right tool", "Synthesizing/Acting", "Common mistakes").
4. A "Related skills" section cross-linking companion skills (e.g. a diagnosis skill pointing to the action skill that should follow it).

Keep it concise — see the generic `create-skill` guidance (progressive disclosure, avoid restating what a capable agent already knows) for general SKILL.md hygiene; this skill only adds the Jumon-specific tool-agnostic rule on top.

## When reviewing an existing skill for staleness

Grep the skill body for prefixes like `linkedin_`, `google_`, `meta_`, `microsoft_`, `reddit_`, or any other provider prefix — any match outside the frontmatter `description` is a bug. Also check for negative capability claims ("is read-only", "not supported", "only X can") and bare numbers next to words like "limit", "guardrail", "cap", or "%" — replace with the discovery/behavior phrasing above.

## After changing a skill

Bump `metadata.version` in the skill's frontmatter. This repo commits directly to `main` (no feature branch) per repo convention — see the root `README.md`/`AGENTS.md` if one exists, otherwise follow the existing git history's pattern.
