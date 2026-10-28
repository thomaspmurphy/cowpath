# cowpath

```
 _________________________________________________
/ A Claude Code skill that reads your own session \
| history, finds the work you keep repeating, and |
| turns it into new skills, or into edits to      |
\ skills you already have.                        /
 -------------------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

The name comes from "pave the cowpaths": build the path where people already walk.

## How it works

`/cowpath` (optionally `/cowpath last 60 days`):

1. **Find.** Groups recent session titles into themes, then counts how many sessions each theme appears in, using only what you typed. A theme needs 3+ sessions on 2+ days.
2. **Filter.** Drops themes an existing skill covers (or proposes an edit to that skill) and themes Claude handles without being told. What survives is what you had to tell Claude: corrections, paths, commands, conventions, preferences restated across sessions.
3. **Propose.** Shows each candidate with its sessions and dates as evidence. You pick; nothing is written before that.
4. **Write.** Writes the skills you picked, following the [skill-diet](https://github.com/nicobailon/skill-diet) principles: each fact on the lowest level that reaches the agent in time, nothing the model already knows, commands as plain usage lines, rules without the story.

It is user-invoked only (`disable-model-invocation: true`), so its description costs nothing in sessions that don't use it.

## Install

```sh
git clone https://github.com/thomaspmurphy/cowpath ~/.claude/skills/cowpath
```

## Requirements

- [`jq`](https://jqlang.org)
- [`memex`](https://github.com/nicosuave/memex) (recommended): indexes and searches your agent history. With it, cowpath gets lexical and semantic search across sessions and machines. Run `memex index` once after installing.

Without memex, cowpath falls back to reading Claude Code's transcripts in `~/.claude/projects/` with `rg` (or `grep`) and `jq`. See `references/no-memex.md`. The fallback has no semantic search and dates sessions by file modification time.
