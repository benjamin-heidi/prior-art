# Prior art

`prior-art` is an Agent Skill. It finds how open-source projects and industry leaders already solve a problem, checks the facts that decide the choice against official docs and source code, and returns a shortlist you choose from. One run stays within 10 web calls and 15 `gh` calls.

## Why not deep research or plain recall

Deep-research skills start 3 to 6 researcher agents and a report writer. Their own guides allow 10 to 15 web calls per researcher, so one question costs 30 to 90 web calls. A model with no research costs almost nothing, but it misses projects released after its training and gets versions, licenses and limits wrong. `prior-art` uses one agent with a fixed call budget and checks every fact the pick depends on.

## What you get

The reply opens with a frame: the decision, the hard constraints as yes-or-no tests, the 2 or 3 deciders (facts that would change the pick), and 2 to 4 approach families. It ends with a table of 3 or 4 candidates, an answer or "open" for each decider, a pick with the condition that would make it wrong, and the cheapest next probe.

Each claim has a tag: `code`, `docs`, `thread`, `secondary` or `recalled`. The pick uses only `code` and `docs` claims. [SKILL.md](skills/prior-art/SKILL.md) defines each tag and every step.

## Measured cost and accuracy

| Question | Web calls | `gh` calls | Time | Tokens in Claude Code |
| --- | --- | --- | --- | --- |
| Encrypt a SQLite database at rest in a Rust desktop app | 10 | 15 | 10 min | about 130k |
| Rate-limit LLM calls across async Python workers | 7 | 14 | 11 min | about 155k |

A hand check of 20 load-bearing claims from three test runs, one of them on an earlier draft, found all 20 correct, including exact file and line numbers. The questions the skill marked open were open in the sources too.

## Requirements

- An agent that can search and fetch web pages. The skill was tested in Claude Code. It uses only web, `gh` and shell commands, so other agents that load `SKILL.md` skills, such as Codex and pi, can run it, but those runs are untested.
- The [GitHub CLI](https://cli.github.com/) (`gh`), signed in. Without it, each repo read costs a web call.

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

The skill runs only when you call it, so its description adds nothing to the context of other sessions.

## Use

```text
/prior-art Encrypt a local SQLite database at rest in a Rust desktop app. Constraints: rusqlite, macOS and Windows, key in the OS keychain, permissive license, nothing extra for users to install.
```

In Codex, write `$prior-art`. In pi, write `/skill:prior-art`.

Start it in a fresh session. In a long session, it hands the scan to one subagent, because every call in the main session re-reads the whole context.

## License

MIT
