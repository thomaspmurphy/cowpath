---
name: cowpath
description: Mine Claude Code session history for work the user keeps repeating, and turn the recurring patterns into new skills or edits to existing ones.
disable-model-invocation: true
---

# cowpath

Pave the paths the user already walks. Default window is the last 30 days; take another if given. If `memex` is not installed, use `references/no-memex.md` for every memex step below.

## 1. Find candidates

```
memex sessions --source claude --since <date> --limit 500 --non-interactive | jq -r 'select(.conversation_kind=="main") | [.repo_project, .label] | @tsv'
memex search "<theme>" --role user --unique-session --since <date> --fields session_id,project,ts,snippet --non-interactive
```

Cluster the labels into themes, then confirm each with a user-role search using a few phrasings (`--query` adds more). A candidate needs 3+ sessions on 2+ days. `sub:*` labels are delegated children; count them with their parent's theme. Subagent prompts index as `--role user` too: drop hits whose `project` is `subagents`.

## 2. Filter

- Drop themes an existing skill covers (`~/.claude/skills/*/SKILL.md`, the project's `.claude/skills/`, installed plugins). If that skill missed something the user had to supply, propose an edit to it instead.
- Drop themes the model already handles without instructions. A skill earns its place only through what the user had to tell Claude: corrections, paths, commands, conventions, ordering, the same preference restated across sessions.

Read 2 or 3 sessions per survivor to collect exactly that (`memex session <id> --max-chars 20000 --non-interactive`, `memex context --record-id <id> --format text` around a hit). Run `memex <cmd> --help` for anything else.

## 3. Propose

Show each candidate: name, sessions and dates as evidence, the trigger, and what it would hold. The user picks. Write nothing before that.

## 4. Write

Follow skill-diet:

- Put each fact on the lowest level that reaches the agent in time: description (one trigger per case), SKILL.md (what every run needs), `references/` (what only some runs need), the tool's own `--help` and errors.
- `disable-model-invocation: true` when only the user starts it.
- Cut what the model already knows and what only a human needs. Never copy a tool's reference.
- Commands as plain usage lines, not tables or schemas.
- Write the rule, not the story. The why goes in the commit message.
- When a clearer error or check in the user's own tool would prevent the mistake, suggest that instead of a skill line.

New skills go in `~/.claude/skills/<name>/`, or the project's `.claude/skills/` when they only apply there. Report each one's line count.
