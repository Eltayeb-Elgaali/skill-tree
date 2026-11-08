# Working roots in a Claude Code session

## Summary

A session has exactly one **working root**: the directory you were in when you started `claude`. It decides where relative paths resolve, where project settings (`.claude/settings.json`) and project instructions come from, and which paths the tools will touch. Paths outside it are refused until you add them as extra roots.

Real work often spans several trees (a service repo plus its schema repo, shared fixtures, a sibling folder of generated clients), so a session can be given additional roots.

## Three ways to add a root

| Way | When it applies | Where it lives |
| --- | --- | --- |
| `--add-dir` at launch | This session only | The command line |
| `/add-dir` mid-session | From now until the session ends | Typed at the prompt |
| `additionalDirectories` | Every session in this project | The `permissions` block of `settings.json` |

- The launch flag accepts several paths at once, so one session can span a service, its schema, and its fixtures.
- The in-session command fires the `DirectoryAdded` hook event, which lets an audit trail record a root being opened mid-flight.
- Background sessions get the same treatment through `claude agents --add-dir`.

## What an added root brings, and what it does not

| It grants | It does not move |
| --- | --- |
| File access: `Read`, `Glob`, `Grep`, `Edit` and `Write` treat paths under the added directory as in bounds | The added directory's own `.claude/settings.json` |
| Skills stored in a `.claude/skills` folder under the added root | Its permission rules and its project identity |

Settings still resolve from the working root. That is deliberate: opening a directory should not silently import somebody else's allow list.

## Seeing the current roots

A status line can print the roots a session is working with:

| Field | Meaning |
| --- | --- |
| `workspace.current_dir` | Where the session is now |
| `workspace.project_dir` | The working root the session launched in |
| `workspace.added_dirs` | The extra grants |

On machines where sessions routinely span repos, showing `workspace.added_dirs` in the status line is the cheapest way to keep the current blast radius visible.

## Commands this node introduces

- The `--add-dir` launch flag
- The `/add-dir` in-session command
- The `additionalDirectories` key inside the `permissions` block of `settings.json`
