# Claude Code Settings Files

Claude Code reads configuration from several sources. This lesson covers three common user and project settings files. Organization-managed settings and command-line options can also affect the final configuration.

## Three Common Files

| File | Scope | Committed to git? |
|------|-------|-------------------|
| `~/.claude/settings.json` | Your user defaults, every project | No, it is your home dir |
| `.claude/settings.json` | This project, shared with the team | Yes, commit it |
| `.claude/settings.local.json` | This project, just you | No, it is gitignored |

- The **user** file holds your personal defaults.
- The **project** file holds settings the whole team should share, so you commit it.
- The **local** file holds your own overrides for that project. Claude keeps it out of git so your private tweaks never land in a pull request.

## What Goes Inside

All three use the same shape. Common keys:

```json
{
  "model": "sonnet",
  "permissions": {
    "allow": ["Bash(git status:*)", "Read", "Edit"],
    "deny": ["Bash(rm:*)"]
  },
  "env": { "MY_FLAG": "1" },
  "hooks": { }
}
```

- `model`: picks which Claude model runs.
- `permissions.allow` and `permissions.deny`: lists of rule strings that grant approval or block matching actions.
- `env`: sets environment variables for the session.
- `hooks`: connects handlers, such as shell commands, to events.

## How They Combine

- For scalar values across these three files, **local** overrides **project**, and **project** overrides **user**.
- Permission lists **merge** instead of overriding.
- Managed policies and command-line options have higher precedence.

So a team can set a shared model in `.claude/settings.json`, and you can quietly override just that value for yourself in `.claude/settings.local.json` without touching what everyone else sees.