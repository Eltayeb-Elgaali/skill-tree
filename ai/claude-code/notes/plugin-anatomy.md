# Claude Code Plugins

## What a plugin is

Skills, slash commands, hooks, subagents, and MCP servers each solve one problem. A **plugin** packages any mix of them into one directory that installs as a unit. One install gives a teammate your commands, hooks, and skills at once, instead of five copy-paste steps.

## Layout

A plugin is a folder with one required file, the manifest at `.claude-plugin/plugin.json`. Everything else is optional and lives in well-known subfolders:

```
review-helper/
  .claude-plugin/
    plugin.json        # required manifest
  commands/            # slash commands, one .md file per command
    checklist.md
  agents/              # subagent definitions
  skills/              # one folder per skill
    standup/
      SKILL.md
  hooks/
    hooks.json         # hook wiring
```

## Manifest

```json
{
  "name": "review-helper",
  "description": "Commands and skills for code review chores",
  "version": "0.1.0"
}
```

- Only `name` is required.
- Keep `description` and `version` anyway, because listings and upgrades depend on them.

## What installing does

- Installing makes the plugin's pieces behave as if you had written them locally.
- Commands appear in the slash menu.
- Skills load when they are relevant.
- Hooks fire on matching events.
- The formats are not new: command files and `SKILL.md` files inside a plugin are the same ones you already write. A plugin is packaging, not a new capability.

## Security

- Hooks run shell commands, and an installed plugin's hooks run with your permissions, the same as your own.
- Treat a plugin like any other code you would execute on your machine: read it first.