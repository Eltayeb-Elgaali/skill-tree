# Hooks in Claude Code

A **hook** runs automatically on a lifecycle event in Claude Code. This lesson focuses on **command hooks**, which execute shell commands. You don't call a hook yourself; Claude Code fires it for you when the event happens. Hooks are how you bolt your own automation onto the agent: format a file the moment it is edited, block a risky command before it runs, or log every prompt.

## The Lifecycle Events

Each hook is tied to an event. The common ones:

| Event | Fires when |
|-------|------------|
| `PreToolUse` | Before a tool runs (can block it) |
| `PostToolUse` | After a tool finishes |
| `UserPromptSubmit` | When you submit a prompt |
| `Stop` | When Claude finishes responding |
| `SessionStart` | When a session begins |

There are more, but these cover most needs.

## Where Hooks Are Configured

Hooks live in `settings.json` under a `hooks` key. Each event maps to a list of **matchers**, and each matcher carries **hook handlers**.

This `PostToolUse` command hook prints the edited file path from the JSON supplied on standard input. It requires `jq` to be installed:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          { "type": "command", "command": "jq -r '.tool_input.file_path'" }
        ]
      }
    ]
  }
}
```

Read it inside out:

- **Event:** `PostToolUse`
- **Matcher:** picks which tool triggers it (here `Edit`)
- **Handler:** each entry in the inner `hooks` array is a command, with `"type": "command"` and the shell command to run

## Typical Uses

- Auto-format or lint a file right after Claude edits it (`PostToolUse`).
- Block a dangerous command before it runs (`PreToolUse`).
- Append every submitted prompt to an audit log (`UserPromptSubmit`).

> **Warning:** A command hook runs with your shell's access, so review its commands carefully.

## Next Step

Add a `PostToolUse` hook of your own to a project's `settings.json`.