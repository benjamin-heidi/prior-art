# Prior Art

> A lean Agent Skill that finds how open source and industry leaders already solve your problem, proves the deciding facts from live sources, and hands back a shortlist you choose from.

## The problem

Deep-research tools answer "what are my options?" with many agents and a long report. A typical run spawns 3 to 6 researchers plus a writer; at the 10 to 15 calls per researcher their own guides set, that is 30 to 90 web calls. Asking a model alone is cheap but stale: it misses new projects and gets versions, licenses and limits wrong.

## What this skill does

One agent, one pass, a hard budget: 10 web calls and 15 `gh` calls.

| Step | What happens |
| --- | --- |
| **Frame** | The decision, hard constraints as yes-or-no tests, the 2 or 3 **deciders** (facts that would change the pick), and 2 to 4 approach **families**. |
| **Discover** | Two parallel web searches (open source, industry), one follow-up in the community's own terms, plus up to 3 candidates from model memory. |
| **Verify** | For the best 3 or 4: maturity, how it works from official docs and the code lines that prove it, and the most-reacted issues as counter-evidence. |
| **Shortlist** | A comparison table, each decider answered or open, one pick with the condition that would make it wrong, and the cheapest next probe. |

Every claim carries a tag, so you see what is proven and what is memory:

| Tag | Source |
| --- | --- |
| `code` | Source code read or a command run in this session |
| `docs` | Official docs or release notes |
| `thread` | Issues, PRs, maintainer comments |
| `secondary` | Blogs, papers, search summaries |
| `recalled` | Model memory only |

The pick rests on `code` and `docs` claims.

## Measured cost

Tested on three real engineering questions: encrypting SQLite in a Rust desktop app, pruning tool results in a long-running LLM agent, and client-side rate limiting of LLM calls across async workers.

| Run | Web calls | `gh` calls | Time | Tokens (Claude Code) |
| --- | --- | --- | --- | --- |
| First draft, no `gh` cap | 10 | 37 to 70 | 12 to 15 min | about 150k |
| With the `gh` cap (SQLite) | 10 | 15 | 10 min | about 130k |
| Published wording (rate limiting) | 7 | 14 | 11 min | about 155k |

Each capped run ended with a pick backed by `code` and `docs` evidence, the condition that would make it wrong, and a concrete next probe.

## Checked for correctness

After the runs, 20 load-bearing claims were checked by hand against their sources: release dates, stars, licenses, default values, file and line references, and quotes from vendor docs. All 20 held, including exact line numbers. Questions the skill marked open were open in the sources too.

## Requirements

- An agent with web search and fetch tools (Claude Code, Codex, pi, Cursor, and others).
- The [GitHub CLI](https://cli.github.com/) (`gh`), signed in. Without it, the skill falls back to web fetches and covers less code.

## Install

With [skills.sh](https://skills.sh):

```bash
pnpm dlx skills add benjamin-heidi/prior-art -g   # or: npx skills add benjamin-heidi/prior-art -g
```

Or copy the folder by hand:

```bash
git clone https://github.com/benjamin-heidi/prior-art.git
cp -R prior-art/skills/prior-art ~/.agents/skills/   # Codex, pi
cp -R prior-art/skills/prior-art ~/.claude/skills/   # Claude Code
```

The skill is slash-only: it runs when you call it, and its description costs no context in other sessions.

## Use

```text
/prior-art Encrypt a local SQLite database at rest in a Rust desktop app. Constraints: rusqlite, macOS and Windows, key in the OS keychain, permissive license, nothing extra for users to install.
```

In Codex, write `$prior-art …`. In pi, write `/skill:prior-art …`.

Start it in a fresh session. In a long session, it hands the scan to one subagent so the session context is not re-read on every call.

## License

MIT
