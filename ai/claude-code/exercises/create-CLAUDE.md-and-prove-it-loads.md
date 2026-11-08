# Task: Create CLAUDE.md and Prove It Loads

## Task

`/root/notes-cli` holds a small note-taking script (`notes.py`). Give the project standing instructions, then prove a fresh run actually obeys them.

### Part 1: the instruction file

Create `/root/notes-cli/CLAUDE.md` containing at least:

- One sentence describing what the project does
- This rule, word for word: `Start every reply with the exact line: RULES LOADED`
- A rule telling the agent to run scripts with `python3` (the word `python3` must appear in the file)

Write it by hand, or run `/init` in the console from `/root/notes-cli` and edit the draft until it satisfies the three requirements.

### Part 2: the obedience test

From inside `/root/notes-cli`, run one small headless prompt (for example, asking what the project does) and save the reply to `/root/notes-cli/reply.txt`.

> [!IMPORTANT]
> Do **not** mention the marker in your prompt. If the instructions loaded, the reply starts with `RULES LOADED` anyway.

### Deliverables

- [ ] `/root/notes-cli/CLAUDE.md`
- [ ] `/root/notes-cli/reply.txt`

---

## Solution

### The idea

This is the **marker rule** pattern. The rule gives the agent an observable behavior. If a fresh run that never mentions the marker still starts with it, the file must have loaded.

### 1. Go to the project

```bash
cd /root/notes-cli
```

Claude Code reads `CLAUDE.md` from the directory you launch it in, so start here.

### 2. (Optional) Look at the script

```bash
cat notes.py
```

This lets you write an accurate one-sentence description.

### 3. Create CLAUDE.md

```bash
cat > CLAUDE.md <<'EOF'
# Notes CLI

A small command-line tool for taking and managing notes, implemented in notes.py.

## Rules

- Start every reply with the exact line: RULES LOADED
- Run scripts with python3, never python.
EOF
```

The quoted `'EOF'` stops the shell from expanding anything inside the file.

### 4. Run the obedience test

Keep the prompt free of any mention of the marker:

```bash
claude -p "What does this project do?" > reply.txt
```

### 5. Verify

```bash
cat CLAUDE.md                          # three requirements present?
grep -c "python3" CLAUDE.md            # at least 1
head -1 reply.txt                      # must print exactly: RULES LOADED
grep -n "RULES LOADED" reply.txt       # should match on line 1
```

### Requirements checklist

| Requirement | Where it is satisfied |
|---|---|
| One sentence describing the project | The line under `# Notes CLI` |
| Exact marker rule | `Start every reply with the exact line: RULES LOADED` |
| `python3` appears | `Run scripts with python3, never python.` |
| Reply starts with the marker | First line of `reply.txt` |

### Common pitfalls

| Pitfall | Fix |
|---|---|
| Running the prompt from the wrong directory | `cd /root/notes-cli` first, or `CLAUDE.md` won't load |
| Mentioning `RULES LOADED` in the prompt | Remove it. The test only proves anything if the prompt never hints at the marker |
| Misspelling the rule | Copy it word for word, including the colon |
| File named `claude.md` or `CLAUDE.MD` | The name must be exactly `CLAUDE.md` |
| Marker missing from `reply.txt` | Check the filename and location, then rerun. Models follow the rule very reliably, so a miss usually means the file didn't load |
| Treating it as enforcement | `CLAUDE.md` is guidance. Hard limits belong in the permission system |