# Compaction and the Handoff File

## Part 1: Concept

### The problem

A conversation can only grow so large. When the context window nears its limit, Claude Code **compacts** it: older turns are replaced by a written summary, and the session continues with that summary plus the most recent turns.

- Automatic: happens when the window gets close to its limit
- Manual: run `/compact` in the console

You can steer the summary with instructions, for example:

```text
/compact keep the failing test names and the list of files already edited
```

### What survives and what doesn't

| Kept | Lost |
|---|---|
| A summary of the earlier conversation | The exact earlier turns |
| The most recent turns | Exact wording, long command output |
| Every file on disk, all git state | Any detail the summary skipped |

> [!WARNING]
> Compaction is **lossy**. The summary may drop the one detail you needed, and the agent will confidently carry on without it. It buys room, not memory.

### The handoff habit

Disk survives; conversation doesn't. Before details get summarized away, ask the agent to **write a handoff note to a file**. A new (or compacted) session reads it and resumes with full fidelity.

A good handoff note has:

1. A **fixed first line**, so tooling and teammates can spot it
2. The **files involved**, by name
3. A section of **concrete next steps**

It should also say what was done and what was learned.

---

## Part 2: Task, Write a Session Handoff File

### Goal

Get Claude to read `/root/todo-app` and write `/root/todo-app/HANDOFF.md` **itself**, so a brand new session could resume from it with no conversation history.

### Project

| File | Purpose |
|---|---|
| `/root/todo-app/app.py` | Small todo CLI |
| `/root/todo-app/TASKS.md` | List of pending work |

### Requirements

| Requirement | Exact marker |
|---|---|
| First line | `# Handoff` |
| Names every file it covers | `app.py` must appear |
| Pending work section | `## Next steps`, listing the pending items from `TASKS.md` |

> [!IMPORTANT]
> `HANDOFF.md` must be written by the agent, not typed by hand. Ground it in the real contents of `app.py` and `TASKS.md`.

### Deliverable

- [ ] `/root/todo-app/HANDOFF.md`

### Option A: headless (simplest)

#### 1. Go to the project

```bash
cd /root/todo-app
```

#### 2. Inspect the inputs (optional, for checking later)

```bash
cat app.py
cat TASKS.md
```

#### 3. Ask the agent and redirect its reply into the file

```bash
claude -p "Read app.py and TASKS.md in this directory. Then write a handoff note that a brand new session could resume from with no other context.

Requirements:
- The first line must be exactly: # Handoff
- Name every project file it covers (app.py and TASKS.md).
- Summarize what app.py actually does, based on its real contents.
- Include a section headed exactly: ## Next steps
- Under '## Next steps', list every pending item from TASKS.md.

Reply with ONLY the markdown for the file: no preamble, no closing remarks, no code fences." > HANDOFF.md
```

> [!NOTE]
> In headless mode the whole reply goes into the file. The last line of the prompt prevents a preamble like "Sure, here's your note:" from breaking the `# Handoff` first-line check.

### Option B: console

1. Run `claude` inside `/root/todo-app`.
2. Prompt it:

   > Read app.py and TASKS.md, then write HANDOFF.md. First line exactly `# Handoff`, name app.py and TASKS.md, summarize what app.py does, and add a `## Next steps` section listing every pending item from TASKS.md.

3. Approve the file write when asked.

### Verify

```bash
head -1 HANDOFF.md              # must print exactly: # Handoff
grep -n "app.py" HANDOFF.md     # must match at least once
grep -n "^## Next steps" HANDOFF.md
cat TASKS.md                    # is every pending item in Next steps?
```

If the first line is wrong, **rerun the command** instead of editing the file by hand.

### Expected shape

```markdown
# Handoff

## Files covered
- `app.py`: what the todo CLI does (from its real contents)
- `TASKS.md`: the list of pending work

## What was done
...

## Next steps
- [pending item 1 from TASKS.md]
- [pending item 2 from TASKS.md]
```

### Common pitfalls

| Pitfall | Fix |
|---|---|
| Preamble like "Here is the note:" lands in the file | Say "reply with ONLY the markdown" and rerun |
| Reply wrapped in a code fence | Say "no code fences" and rerun |
| Running from the wrong directory | `cd /root/todo-app` first so the agent finds both files |
| Next steps invented instead of copied | Tell the agent to list the items from `TASKS.md`, then compare with `cat TASKS.md` |
| Fixing the file by hand | The task requires the agent to write it, so rerun instead |