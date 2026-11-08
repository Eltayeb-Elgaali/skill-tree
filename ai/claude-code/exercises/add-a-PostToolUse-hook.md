# Task: Add a `PostToolUse` Hook

## Goal

Give the `hookproj` project a `settings.json` with one hook: a `PostToolUse` hook that matches `Edit` and runs a command whenever Claude edits a file.

## Practice

Hooks live under a `hooks` key in `settings.json`. Make the directory:

```bash
mkdir -p /root/hookproj/.claude
```

Then write the settings file. The command below just logs that an edit happened. Any shell command works:

```bash
cat > /root/hookproj/.claude/settings.json <<'EOF'
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          { "type": "command", "command": "echo edited >> /root/hookproj/edits.log" }
        ]
      }
    ]
  }
}
EOF
```

Keep the JSON valid: a trailing comma or missing brace will fail the check. Confirm it parses:

```bash
jq . /root/hookproj/.claude/settings.json
```

## Expected Result

A valid `settings.json` at `/root/hookproj/.claude/` with a `hooks` block for at least one event, and at least one `command` string inside it.