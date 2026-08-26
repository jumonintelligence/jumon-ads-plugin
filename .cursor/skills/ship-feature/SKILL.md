---
name: ship-feature
description: Execute a Jumon Notion ticket in this repo end to end, from branch through skill edits and validation to PR, then write the result back to the ticket. Use whenever an agent is handed a "Jumon Tasks" Notion ticket scoped to jumon-ads-plugin, or is asked to "implement this ticket" or "ship this feature" here.
---

# Ship a feature (jumon-ads-plugin)

This repo is a Claude plugin marketplace: markdown skills and JSON manifests, no build,
no test suite, no CI. The workflow below is the trimmed version of the one in
`mcp-ads-manager` and `jumon-mcp`. Everything about the ticket contract is identical.

## The contract

The Notion ticket is the specification. It was designed by Claude Cowork against this
repo before you were launched. Execute it, do not redesign it.

- The ticket's out-of-scope line and "Explicitly unchanged" list are binding.
- **If the ticket is ambiguous, incomplete, or contradicts what you find here, stop.**
  Set Status to `Blocked`, comment naming exactly what is missing, and end the run.
  Never guess.

## Step 1: Read the ticket

Fetch it through the Notion MCP. Confirm goal and scope, architecture, implementation
sequence, docs to update, acceptance criteria, and that open questions are empty. Any
gap is a `Blocked`. Set Status to `In Progress` once it passes.

## Step 2: Branch

**This repo has no `develop` branch.** Base is `main` unless the ticket's `Base branch`
field says otherwise.

```bash
git fetch origin
git stash push -u -m "pre-ticket"   # only if the tree is dirty
git checkout main && git pull --ff-only origin main
git checkout -b <type>/<short-slug>
```

## Step 3: Implement

Skills live at `plugins/jumon-ads/skills/<name>/SKILL.md`. Follow the ticket's
architecture section. When authoring or editing a skill, follow
[`.cursor/skills/create-jumon-skill/SKILL.md`](../create-jumon-skill/SKILL.md).

A skill's `description` is what decides whether it ever triggers. Treat a description
change as a behaviour change, not a copy edit.

## Step 4: Docs and manifests

Update every file the ticket names. A new skill also means:

- `plugins/jumon-ads/.claude-plugin/plugin.json` if the manifest enumerates skills
- `README.md` "What's included"
- the plugin docs in `mcp-ads-manager` at `docs/claude-plugin.md`, when the change is
  user-visible

## Step 5: Cleanup and validation

Review `git diff main...HEAD` in full. Remove leftover drafts, dead links and stale
examples. Then validate by hand, since nothing else will:

- Every JSON file parses: `for f in $(git ls-files '*.json'); do python3 -m json.tool "$f" >/dev/null || echo "BAD $f"; done`
- Every changed `SKILL.md` has valid frontmatter with `name` and `description`
- Every relative link in a changed file resolves

## Step 6: Commit, push, open the PR

Conventional Commits title (`feat(skills): ...`). Body with `## Summary`, `## Changes`,
`## Testing`, `## Risk / Rollback`, matching the `standard-pr` shape used in the other
two repos. The body must carry the ticket link:

```
Ticket: <the Notion page URL from the ticket you were handed>
```

PR targets `main`. Do not merge it yourself.

## Step 7: Write the result back to the ticket

Through the Notion MCP, on the same ticket: `Status` = `PR Created`, `PR` = the PR URL,
`Agent Run` = the Cursor cloud agent URL when the run exposes one. Add a comment with a
one-line summary.

The ticket stays in `PR Created` through review. Juanes merges manually. Never move a
ticket to `Done` yourself.
