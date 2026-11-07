---
name: prior-art
description: Scan how open source and industry leaders already solve a problem, within a fixed call budget. Returns a sourced shortlist to choose from.
disable-model-invocation: true
---

# Prior art

Find how others already solve the problem, prove the facts that decide the choice from live sources, and hand back a **shortlist** the user chooses from. Everything goes in the reply.

## Rules

- **Budget**: at most 10 web calls (search or fetch) and 15 `gh` calls. Each command counts once, failed ones too. Without web tools, use `curl`; without `gh`, a repo page fetch counts as a web call. Local reads (repo files, package caches) are free; keep them targeted. When the budget runs out, or every decider is answered, write the shortlist; open questions become the next probe.
- **Read lines, not files**: pipe every read, for example `gh api repos/<o>/<r>/contents/<path> --jq .content | base64 -d | grep -n '<keyword>'`, then `sed -n '<from>,<to>p'`. To search one file several ways, fetch it once into a shell variable in the same command. Give each web fetch a specific question; a page summary drops detail. When a fetch misses, read the docs source in its repo through `gh` before spending another web call.
- **Stale knowledge**: your recall, memory notes and repo docs suggest candidates; only sources read this run settle facts. When any of them holds an earlier conclusion on this question, say so and test it like a candidate; if it fails a constraint, keep it as a baseline row.
- **Evidence order**: official docs and release notes first; when the code is public, the same claim in the code. Blogs, papers and comparisons serve discovery.
- **Context**: run inline in a fresh session. When this session already holds a long task, give the steps to one subagent with the frame, and relay its shortlist.

## Steps

1. **Frame.** In the reply, write:
   - the decision in one sentence;
   - the hard constraints, each as a yes-or-no test, with your reading of any fuzzy one;
   - your assumption for any open question about what the user wants;
   - the **deciders**: the 2 or 3 facts about the candidates that would change the pick;
   - 2 to 4 approach **families**, distinct ways to solve it.

   Check the current repo's dependency files for what it already uses. Done when every constraint is a yes-or-no test and no two families overlap.
2. **Discover.** Run 2 web searches in parallel: open-source projects, and how industry leaders solve it (vendor docs, engineering blogs, talks). When the results name the problem in other terms, run 1 more search with them. Add up to 3 candidates from recall. Done when you hold 4 to 6 candidates, at least one from search, and each family has a candidate or is marked empty.
3. **Verify.** Keep the strongest 3 or 4. One candidate is one approach: its integration routes, or one feature per vendor, are variants in the same row. Drop the rest with a one-line reason. Baseline rows (what the repo already has, an earlier conclusion) sit outside these counts. For each kept candidate:
   - Maturity: for a repo, `gh api repos/<o>/<r>` (stars, last push, license, archived) and the latest release or tag date; for a vendor feature, its GA or beta status and date.
   - How it works: from the docs, plus the code lines when public.
   - Counter-evidence: `gh issue list -R <o>/<r> --state all --search "<feature keyword> sort:reactions-+1-desc" --limit 5`, on the repo that owns the integration; for a vendor feature, its limits or known-issues page.

   Spend the remaining budget on the deciders. Done when each kept candidate's "how it works" cites docs, and the claims the pick rests on cite code when it is public.
4. **Shortlist.** Reply with the scan date and:
   - A table: candidate, family, maturity, license or cost, constraints passed, main risk.
   - **How each works**: one line per candidate, with its evidence.
   - **Deciders**: each with its answer and source, or open.
   - **Pick**: one candidate, the condition that would make it wrong, and whether the next probe must pass first. Write **No pick yet** only when no candidate leads.
   - **Next probe**: the cheapest check that settles the pick or the open deciders, such as a spike, a census of your own data, or one doc page.

   Tag every claim: `code` (source, repo file or command output read this session), `docs` (official docs or release notes, fetched or read in their repo), `thread` (issues, PRs, maintainer comments), `secondary` (blogs, papers, search summaries, even when they quote docs) or `recalled`. A decider backed only by `thread` or `secondary` evidence stays open, and its check goes in the next probe. Done when every claim carries a tag and the Pick rests on `code` and `docs`, with any `thread` or `secondary` support named as such.

End with the next steps: map the subsystem where the pick lands (for example with a `how` skill, if you have one), then design the seam (for example with `codebase-design`).
