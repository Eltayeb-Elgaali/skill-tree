# Task: Project Rules vs User Rules

## Task

Set up deliberately conflicting reply-style rules at the user level and the project level, then run the agent once and see which rule wins.

### Files to create

| File | Level | Must contain |
|---|---|---|
| `/root/.claude/CLAUDE.md` | User | The rule: `End every reply with the exact line: STYLE: user-level` |
| `/root/precedence-lab/CLAUDE.md` | Project | A sentence stating this project overrides any user-level reply style, the rule `End every reply with the exact line: STYLE: project-level`, and an import line `@style.md` |
| `/root/precedence-lab/style.md` | Imported | At least one extra style rule of your choosing (for example, a limit on reply length) |

### The test

From inside `/root/precedence-lab`, run one small headless prompt (any harmless question) and save the reply to `/root/precedence-lab/winner.txt`.

> [!IMPORTANT]
> Do **not** mention either marker in your prompt. Both instruction files load, the project file declares the override, and the reply should end with the project marker.

### Deliverables

- [ ] `/root/.claude/CLAUDE.md`
- [ ] `/root/precedence-lab/CLAUDE.md`
- [ ] `/root/precedence-lab/style.md`
- [ ] `/root/precedence-lab/winner.txt`

---

## Solution

### The idea

All instruction levels load together. When two levels give opposing orders, the more specific level wins (**project beats user**). The project file also says so explicitly, which removes any ambiguity for the model.

### 1. Create the user-level file

```bash
mkdir -p /root/.claude
cat > /root/.claude/CLAUDE.md <<'EOF'
# Personal preferences

- End every reply with the exact line: STYLE: user-level
EOF
```

### 2. Create the project folder and the imported file

```bash
mkdir -p /root/precedence-lab
cd /root/precedence-lab

cat > style.md <<'EOF'
# Extra style rules

- Keep every reply under 80 words.
EOF
```

### 3. Create the project-level file

```bash
cat > CLAUDE.md <<'EOF'
# Precedence Lab

This project overrides any user-level reply style.

- End every reply with the exact line: STYLE: project-level

@style.md
EOF
```

`@style.md` is the import line. It pulls the contents of `style.md` into the instructions.

### 4. Run the test

Launch from inside the project folder, and keep the prompt free of any marker:

```bash
cd /root/precedence-lab
claude -p "What is a shell variable?" > winner.txt
```

### 5. Verify

```bash
tail -n 3 winner.txt                       # last line should be: STYLE: project-level
grep -c "STYLE: project-level" winner.txt  # expect 1
grep -c "STYLE: user-level" winner.txt     # expect 0
wc -w winner.txt                           # roughly under 80 words (style.md rule)
```

### Requirements checklist

| Requirement | Where it is satisfied |
|---|---|
| User-level marker rule | `/root/.claude/CLAUDE.md` |
| Override sentence | `This project overrides any user-level reply style.` |
| Opposing project rule | `End every reply with the exact line: STYLE: project-level` |
| Import line | `@style.md` in the project `CLAUDE.md` |
| Extra style rule | `Keep every reply under 80 words.` in `style.md` |
| Reply ends with project marker | Last line of `winner.txt` |

### Common pitfalls

| Pitfall | Fix |
|---|---|
| Running the prompt from the wrong directory | `cd /root/precedence-lab` first, or the project file won't load |
| Mentioning a marker in the prompt | Remove it. The test only proves something if the prompt gives no hint |
| Forgetting `mkdir -p /root/.claude` | The heredoc fails if the folder doesn't exist |
| Typo in the marker text | Copy the lines word for word, including the colon and capitalization |
| Both markers in the reply | Make sure the override sentence is in the project file, then rerun |
| Extra rule in `style.md` that clashes with the marker | Keep it neutral (such as a length limit), not something like "no sign-offs" |
| Trailing blank line hides the marker | Check with `tail -n 3`, not just `tail -n 1` |