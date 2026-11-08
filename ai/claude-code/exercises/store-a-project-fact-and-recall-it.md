# Task: store a project fact and recall it

`/root/api-server` is a small service. Its deploy script lives at `/root/api-server/scripts/deploy.sh`, and the correct invocation is `bash` followed by `scripts/deploy.sh --safe` (the flag runs pre-flight checks first). Nothing in the code says so. Store that fact as project memory, then prove a brand new session knows it.

## Part 1: store the fact

Get this into `/root/api-server/CLAUDE.md`:

* a section headed exactly `## How to deploy`
* under it, a line containing exactly `scripts/deploy.sh --safe`

Use whichever path you prefer: the `#` shortcut in a console session started from `/root/api-server`, the `/memory` command, or editing the file directly.

## Part 2: prove recall

Start a fresh headless run from inside `/root/api-server`, ask only for the project's deploy command, and save the reply to `/root/api-server/recall.txt`. Do not restate the command in your prompt; the point is that the memory file supplies it.

---

# Solution

## Part 1: store the fact

Editing the file directly is the scriptable path, so I use a heredoc:

```bash
cd /root/api-server

cat >> CLAUDE.md << 'EOF'

## How to deploy

Deploy with: bash scripts/deploy.sh --safe
(The --safe flag runs pre-flight checks first.)
EOF
```

`>>` appends, so any existing content in `CLAUDE.md` is kept. Check the result:

```bash
cat CLAUDE.md
```

Expected to contain:

```markdown
## How to deploy

Deploy with: bash scripts/deploy.sh --safe
(The --safe flag runs pre-flight checks first.)
```

## Part 2: prove recall

Run a fresh headless session from inside the project folder, so Claude Code loads `./CLAUDE.md`. The prompt does not mention the command itself:

```bash
cd /root/api-server

claude -p "What is this project's deploy command?" > recall.txt
```

* `-p` runs Claude Code in headless (print) mode. It starts a brand new session with no earlier conversation.
* `> recall.txt` saves the reply to `/root/api-server/recall.txt`.

## Verify

```bash
grep -F "scripts/deploy.sh --safe" recall.txt && echo "memory works"
```

If `grep` finds the line, the fact went from `CLAUDE.md` into a fresh session, so memory works end to end.

## Notes

* Run the headless command from `/root/api-server`. From another folder, the project's `CLAUDE.md` would not be loaded.
* The prompt must not contain `scripts/deploy.sh --safe`. Otherwise `grep` would pass even if memory failed.