# Without memex

Transcripts are `~/.claude/projects/<cwd-slug>/<session-id>.jsonl`. Set the window in days with `-mtime -N`.

Session titles (one per file):

```
find ~/.claude/projects -maxdepth 2 -name '*.jsonl' -mtime -30 -print0 | xargs -0 rg -m1 -o '"aiTitle":"[^"]*"'
```

Sessions where the user typed a theme (tool output and skill text excluded):

```
find ~/.claude/projects -maxdepth 2 -name '*.jsonl' -mtime -30 -print0 | xargs -0 rg -il '"role":"user","content":"[^"]*<theme>'
```

Read one session as prompts and replies:

```
jq -r 'select(.type=="user" and (.message.content|type)=="string") .message.content, (select(.type=="assistant") | .message.content[]? | select(.type=="text") | .text)' <file>
```

Without `rg`, use `grep -o` / `grep -il` with the same patterns.
