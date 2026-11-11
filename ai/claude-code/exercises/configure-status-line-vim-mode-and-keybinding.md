# Task

Create the following files:

1. `/root/.claude/statusline.sh` - an executable shell script.
   - It must read the JSON payload from stdin and print **exactly one line** containing the text `agentic-lab`.
   - Include something dynamic on the line, such as the working directory or the model name from the payload.
   - It must still print its line when the payload is an empty JSON object, so give any `jq` lookup a fallback value.

2. `/root/.claude/settings.json` - valid JSON containing:
   - a `statusLine` object whose `type` is `command` and whose `command` is the full path to the script
   - the key that switches the prompt into vim editing, set to `vim`
   - If the file already has other keys, keep them intact.

3. `/root/.claude/keybindings.json` - valid JSON whose `bindings` array holds one block for the `Chat` context, binding the keystroke `ctrl+f` to the action id `history:search`.

Test the status line script by piping a stub payload into it before wiring it in, and run every file through `jq` before submitting; one bad character disables a whole settings file.

# Solution

## Step 1: Create the directory

```bash
mkdir -p /root/.claude
```

## Step 2: Write the status line script

```bash
cat > /root/.claude/statusline.sh <<'EOF'
#!/usr/bin/env bash
input=$(cat)
model=$(printf '%s' "$input" | jq -r '.model.display_name // "unknown-model"')
dir=$(printf '%s' "$input" | jq -r '.workspace.current_dir // .cwd // "unknown-dir"')
printf 'agentic-lab | %s | %s\n' "$model" "$dir"
EOF
chmod +x /root/.claude/statusline.sh
```

Every `jq` lookup has a `// "fallback"`, so an empty `{}` payload still prints a line.

## Step 3: Test the script with stub payloads

```bash
echo '{"model":{"display_name":"Opus"},"workspace":{"current_dir":"/root/proj"}}' | /root/.claude/statusline.sh
echo '{}' | /root/.claude/statusline.sh
echo '{}' | /root/.claude/statusline.sh | wc -l   # must print 1
```

Expected output:

```text
agentic-lab | Opus | /root/proj
agentic-lab | unknown-model | unknown-dir
1
```

## Step 4: Create or merge `settings.json`

This keeps any existing keys intact:

```bash
f=/root/.claude/settings.json
[ -s "$f" ] || echo '{}' > "$f"
jq --arg cmd /root/.claude/statusline.sh \
   '. + {statusLine: {type: "command", command: $cmd}, editorMode: "vim"}' \
   "$f" > "$f.tmp" && mv "$f.tmp" "$f"
```

## Step 5: Create `keybindings.json`

```bash
cat > /root/.claude/keybindings.json <<'EOF'
{
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+f": "history:search"
      }
    }
  ]
}
EOF
```

## Step 6: Validate every file with `jq`

```bash
jq . /root/.claude/settings.json
jq . /root/.claude/keybindings.json
ls -l /root/.claude/statusline.sh   # should show the executable bit (-rwx...)
```

Fix any parse error before submitting.