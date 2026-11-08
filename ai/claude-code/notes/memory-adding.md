# Adding memories

There are three ways to get a fact into a memory file, and they all end in the same place: a line of markdown in a `CLAUDE.md`.

## The `#` shortcut

In the console, start a message with `#` followed by the fact:

```text
# The staging database resets every night at 02:00 UTC
```

Claude Code recognizes the prefix as "remember this" and asks which memory file to store it in: the project's `CLAUDE.md` or your user-level file. Pick the level using the placement logic from hour 011. This is the low-friction path, made for the moment mid-session when you catch yourself explaining something for the second time.

## `/memory`

The `/memory` command opens your memory files in an editor from inside the console. Use it to review what has accumulated, fix stale entries, and delete what no longer earns its token cost. Memory files deserve the same hygiene as code: an outdated "fact" is worse than no entry, because the agent will confidently act on it.

## Editing the file directly

Since memory is just a file, your shell works too: open the `CLAUDE.md` in an editor or append with a heredoc. This is the only path available to scripts, and it is how this topic's solution does it.

## What to store

| Store | Do not store |
|---|---|
| Commands with non-obvious flags | Secrets and credentials |
| Ports, URLs, environment names | Anything that changes