# Checkpoints and /rewind in Claude Code

## How it works

- Before Claude Code edits a file, it automatically snapshots that file's state.
- Checkpoints accumulate throughout the session.
- Run `/rewind` (or press `Esc` twice) to open a picker and restore one.

## Restore options

| Restore | Effect |
|---|---|
| **Code only** | Files return to the checkpoint; the conversation continues |
| **Conversation only** | The chat rewinds; files stay as they are |
| **Both** | Files and conversation return together |

- **Code only** is for when the discussion was productive but the edits went wrong.
- **Conversation only** is for when the files are fine but the conversation went off track.

## What checkpoints do not cover

- **Only file-editing tools are tracked.** Bash side effects (deleting a file, writing to a database, installing a package) are not undone by rewinding.
- **Session-scoped.** Checkpoints are not a durable backup and are not visible to teammates.
- **All-or-nothing.** You can't cherry-pick one file's change out of a snapshot.

`/rewind` is great for letting the agent try something bold mid-session and jumping back if it fails, but it is **not** version control.

## The durable layer: git

- Git covers what checkpoints can't: it survives the session, records intent through commit messages, and handles bash side effects on tracked files.
- Workflow: commit a known-good state before risky work, inspect what changed, and roll back if the experiment fails.
- Since `/rewind` needs the interactive picker, the lab grades the git workflow instead. Practice `/rewind` yourself in an interactive session.