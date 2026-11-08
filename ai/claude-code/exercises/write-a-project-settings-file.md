# Task: Write a Project Settings File

## Goal

Create a project settings file at `/root/cfgproj/.claude/settings.json` that sets a `model` and grants at least one permission rule.

## Practice

Make the project and its `.claude` directory first:

```bash
mkdir -p /root/cfgproj/.claude
cd /root/cfgproj
```

Now write `.claude/settings.json`. It needs a `model` string and a `permissions.allow` array with at least one rule. A minimal version looks like this:

```json
{
  "model": "sonnet",
  "permissions": {
    "allow": ["Read", "Bash(git status:*)"]
  }
}
```

You can create it with your editor, or ask Claude to write it for you. Pick any real model name and any rules you want, as long as `model` is a string and `allow` holds at least one rule.

## Expected Result

`/root/cfgproj/.claude/settings.json` is valid JSON, has a string `model`, and has a non-empty `permissions.allow` array.