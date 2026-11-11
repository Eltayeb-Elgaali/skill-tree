# Status Line

Claude Code can render a one-line status bar at the bottom of the console, and you decide what goes in it. The content comes from a command you supply: Claude Code runs it, and the first line the command prints becomes the status line. People put the current model there, the working directory, the git branch, or anything else a shell script can compute.

## Where it is configured

The status line lives in `settings.json`, the same file that holds your permission rules from hour 006. Two locations matter:

* `/root/.claude/settings.json` applies to you everywhere (user scope).
* A project's own `.claude/settings.json` applies inside that project.

The entry is an object under the `statusLine` key:

```json
{
  "statusLine": {
    "type": "command",
    "command": "/root/.claude/statusline.sh"
  }
}
```

## The command's contract

Each time the status line refreshes, Claude Code runs your command and feeds it a JSON payload on stdin describing the session: the active model, the current working directory, and similar fields. Your script can parse that with `jq`, or ignore stdin entirely and print something static:

```bash
#!/bin/bash
input=$(cat)
model=$(echo "$input" | jq -r '.model.display_name // "unknown"')
echo "[$model] $(pwd)"
```

Reading stdin with `cat` first matters even if you do not use the payload. The exact field names can shift between versions, so the fallback after the `//` operator keeps the script from breaking when a field is missing. The script must be executable, which is what `chmod` with `+x` does:

```bash
chmod +x /root/.claude/statusline.sh
```

A quick sanity test before wiring it up is to pipe a stub payload in and confirm one line comes out:

```bash
echo '{}' | /root/.claude/statusline.sh
```

## Keeping settings.json valid

`settings.json` is strict JSON, with no comments and no trailing commas. One bad character silently disables everything in the file. After every edit, validate it:

```bash
jq . /root/.claude/settings.json
```

If `jq` prints the file back, the JSON parses. If it prints an error, fix the file before starting a session.

## Commands this node introduces

The `statusLine` settings key, `jq` for validating and querying JSON, and `chmod` with `+x` to make a script executable.