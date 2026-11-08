# Writing CLAUDE.md Rules That Hold

## The idea

A CLAUDE.md rule is a sentence the model reads **once per session** and must apply while thinking about something else. Vague rules lose that fight. Specific, imperative, checkable rules win it.

## Do and avoid

| ✅ Do this | ❌ Avoid |
|---|---|
| `Run scripts with python3, never python.` | `Write good, clean code.` |
| `Never edit files under generated/.` | `Be careful with important files.` |
| `Commit messages: one line, imperative mood.` | `Follow best practices for commits.` |

## Three properties of a good rule

| Property | Meaning |
|---|---|
| **Imperative** | State the action, not a philosophy |
| **Specific** | Name the command, path, or format exactly |
| **Checkable** | You could look at the output and say yes or no |

## Testing that instructions load

Guidance you can't verify is guesswork. Use a **marker rule**:

1. Instruct the agent to start every reply with a fixed line.
2. Run a tiny headless prompt from the project directory.
3. Search the reply for the marker.

If the marker appears, the file loaded and the model is following it.

```bash
claude -p "say hello" | grep "YOUR MARKER LINE"
```

> [!NOTE]
> CLAUDE.md is **guidance, not enforcement**. Hard limits (which commands may run, which files may change) belong to the permission system, not to prose.

## Writing multi-line files from the shell

Use a heredoc to write everything between the markers into a file:

```bash
cat > CLAUDE.md <<'EOF'
# Project notes
- Run scripts with python3.
EOF
```

## Commands introduced

| Command | What it does |
|---|---|
| `cat > file <<'EOF' ... EOF` | Writes a multi-line file in one command |
| `grep` | Searches a saved reply to verify a marker rule fired |