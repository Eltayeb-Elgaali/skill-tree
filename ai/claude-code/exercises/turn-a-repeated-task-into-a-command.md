# Task: Turn a repeated task into a command

The project at `/root/todo-scan` contains Python files carrying TODO comments. Listing them is the kind of task you would repeat weekly, so package it as a project slash command and prove the command runs.

## Requirements

- Create the command file `/root/todo-scan/.claude/commands/todo-report.md`.
- Give it YAML frontmatter with a `description` field.
- Write a prompt that asks the agent to find the TODO comments in this project's Python files and to begin its reply with the exact line `TODO REPORT`.
- Run the command headlessly from inside `/root/todo-scan` and save the output to `/root/todo-scan/report.txt`.

### Project contents

`app.py`:

```python
def load_config(path):
    # TODO: validate the config schema before returning
    return open(path).read()

def start():
    # TODO: add a retry loop around the network call
    print("starting")
```

`utils.py`:

```python
def slugify(text):
    # TODO: strip punctuation as well as spaces
    return text.lower().replace(" ", "-")
```

---

# Solution

## Step 1: Create the commands folder

Project commands live in `.claude/commands/` inside the project. The file name (without `.md`) becomes the command name, so `todo-report.md` becomes `/todo-report`.

```bash
mkdir -p /root/todo-scan/.claude/commands
```

## Step 2: Write the command file

The file needs YAML frontmatter with a `description`, then the prompt body.

```bash
cat > /root/todo-scan/.claude/commands/todo-report.md << 'EOF'
---
description: List every TODO comment in this project's Python files
---

Find all TODO comments in the Python (.py) files of this project.

Begin your reply with the exact line:

TODO REPORT

Then list each TODO on its own line in this format:

- <file>:<line number> - <TODO text>

Do not put anything before the line `TODO REPORT`.
EOF
```

## Step 3: Run it headlessly and save the output

Run from inside the project so the command is found and the agent searches the right files. The `--allowedTools` flag lets the agent read and search files without an approval prompt, which headless mode cannot answer.

```bash
cd /root/todo-scan
claude -p "/todo-report" --allowedTools "Read,Grep,Glob" > /root/todo-scan/report.txt
```

If your Claude Code version does not accept the slash command inside `-p`, feed the command file's body in instead:

```bash
cd /root/todo-scan
sed '1,/^---$/{/^---$/!d}' .claude/commands/todo-report.md | sed '1d' \
  | claude -p --allowedTools "Read,Grep,Glob" > /root/todo-scan/report.txt
```

## Step 4: Verify

```bash
head -1 /root/todo-scan/report.txt    # must be exactly: TODO REPORT
cat /root/todo-scan/report.txt        # must list all 3 TODOs
```

## Expected `report.txt`

Wording and line numbers may vary slightly, but it must start with `TODO REPORT` and cover these three TODOs:

```
TODO REPORT

- app.py:2 - validate the config schema before returning
- app.py:6 - add a retry loop around the network call
- utils.py:2 - strip punctuation as well as spaces
```

## Checklist

- [ ] `/root/todo-scan/.claude/commands/todo-report.md` exists
- [ ] It has frontmatter with a `description:` field
- [ ] The prompt asks for the exact first line `TODO REPORT`
- [ ] `/root/todo-scan/report.txt` exists and starts with `TODO REPORT`
- [ ] It lists the TODOs from both `app.py` (2) and `utils.py` (1)