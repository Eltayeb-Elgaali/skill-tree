# Task: Map a Seeded Repo into a Notes File

`/root/mapper` holds a small Python project you have not seen before. Have Claude Code explore it and produce a notes file you could hand to a teammate, then verify the facts in it yourself.

## Artifact

Create one artifact:

- `/root/mapper/NOTES.md` – the agent's reply to a single headless prompt, run from `/root/mapper` and redirected into the file.

## Requirements

The notes must:

- [ ] Begin with the exact line `REPO NOTES`
- [ ] Name the file that defines the function `load_inventory` (the file name must appear in the notes)
- [ ] State the port number set in `config.json` (the number itself, not just the key)

## Notes

- Exploration needs only read tools, so no approvals are involved.
- After the run, open the repo files and confirm the agent got both facts right before you trust the notes.


# Solution: Map a Seeded Repo into a Notes File

## Prerequisites

- Claude Code is installed and authenticated (`claude --version` works).
- `/root/mapper` exists and contains the Python project.

---

## Step 1: Go to the repo

```bash
cd /root/mapper
ls -la
```

Get a quick look at what is there. You are not trusting the agent yet, so this is just orientation.

---

## Step 2: Run Claude Code headless and redirect the output

Use print mode (`-p`) with a single prompt. Restrict the agent to read-only tools so no approvals are needed.

```bash
claude -p "Explore this repository using read-only tools. Reply with a notes file in Markdown. The very first line of your reply must be exactly: REPO NOTES
Then include: (1) a short overview of what the project does, (2) the file that defines the function load_inventory, using its file name, (3) the port number set in config.json, written as the actual number, (4) a short list of the main files and what each does. Output only the notes, with no preamble and no code fences around the whole reply." \
  --allowedTools "Read,Grep,Glob" > NOTES.md
```

**Why these choices**

| Choice | Reason |
| --- | --- |
| `-p` | Headless, single-prompt run. Prints the reply and exits. |
| `--allowedTools "Read,Grep,Glob"` | Read-only exploration, so nothing prompts for approval. |
| `> NOTES.md` | Saves the agent's reply as the artifact. |
| "first line must be exactly `REPO NOTES`" | Satisfies the first requirement. |
| "no preamble" | Stops lines like "Here are the notes:" from appearing before `REPO NOTES`. |

---

## Step 3: Check the format of the notes

```bash
head -n 1 NOTES.md
```

Expected output:

```
REPO NOTES
```

To check for stray whitespace or hidden characters:

```bash
head -n 1 NOTES.md | cat -A
```

Expected output:

```
REPO NOTES$
```

If the first line is anything else (a preamble, a code fence, a heading marker), tighten the prompt and rerun Step 2.

---

## Step 4: Verify the `load_inventory` fact yourself

Find where the function is actually defined:

```bash
grep -rn "def load_inventory" /root/mapper --include="*.py"
```

Note the file name in the result. Beware of files that only **call** or **import** `load_inventory`, since those are not the definition.

Then confirm the notes name that file:

```bash
grep -n "<file_name>.py" NOTES.md
```

Replace `<file_name>` with the file you found.

---

## Step 5: Verify the port number yourself

Open the config directly:

```bash
cat /root/mapper/config.json
```

Or extract just the port:

```bash
grep -n "port" /root/mapper/config.json
```

Note the number. Then confirm it appears in the notes:

```bash
grep -n "<port_number>" NOTES.md
```

Watch for traps:

- More than one port-like key (for example `db_port` and `port`). Use the one the task asks about, the port set in `config.json`.
- A different port mentioned in the code or a README. The config file is the source of truth.

---

## Step 6: Fix the notes if anything is wrong

If the agent got either fact wrong, or the first line is off:

1. Adjust the prompt, for example by adding "check config.json directly" or "search for the `def load_inventory` definition."
2. Rerun Step 2. Note that `>` overwrites the file.
3. Repeat Steps 3 to 5.

Do not hand-edit `NOTES.md`. The artifact is meant to be the agent's reply as produced by the run.

---

## Final Checklist

- [ ] `/root/mapper/NOTES.md` exists
- [ ] It was produced by a single headless `claude -p` run from `/root/mapper`
- [ ] Line 1 is exactly `REPO NOTES`
- [ ] The notes name the file that defines `load_inventory`, and I confirmed it with `grep`
- [ ] The notes state the port number from `config.json`, and I confirmed it by opening the file