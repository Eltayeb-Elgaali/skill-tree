# Task: Build a standup skill

`/root/standup-demo` is a repo with a few days of commit history. Build a personal skill that turns any repo's recent git activity into a standup report, then use it on this repo.

## Part 1: the skill

Create `/root/.claude/skills/standup/SKILL.md` with:

- Frontmatter between `---` lines containing a line reading exactly `name: standup` and a `description:` line saying when to use the skill.
- A body that tells the agent to look at recent git history (name the exact command in the body) and write a report where:
  - the first line is exactly `# Standup`
  - there are sections headed exactly `## Yesterday`, `## Today`, and `## Blockers`

## Part 2: use it

Produce `/root/standup-demo/STANDUP.md` with the skill.

- In the console, start from `/root/standup-demo`, invoke `/standup`, and let the agent write the file (approve the write).
- Headless also works: feed the skill's instructions and the repo's git history into a `-p` run and redirect the output.

Read the result once. `## Yesterday` should reflect the actual commits in this repo, not generic filler.

### Repo contents

`README.md`:

```
# standup-demo
Small parsing utility used for the standup skill lab.
```

`parser.py`:

```python
def parse(line):
    if not line.strip():
        return []
    return line.strip().split(",")
```

### Git history

```
b601bcc 2026-11-07 Lab Learner add README
 README.md | 2 ++
 1 file changed, 2 insertions(+)

683f4a8 2026-11-07 Lab Learner fix crash on empty input
 parser.py | 2 ++
 1 file changed, 2 insertions(+)

21f3abb 2026-11-07 Lab Learner add csv parser
 parser.py | 2 ++
 1 file changed, 2 insertions(+)
```

---

# Solution

## Part 1: create the skill

```bash
mkdir -p /root/.claude/skills/standup
cat > /root/.claude/skills/standup/SKILL.md << 'EOF'
---
name: standup
description: Generate a daily standup report from the recent git history of the current repo. Use when the user asks for a standup, daily update, status report, or a summary of what they worked on recently.
---

# Standup skill

Turn the current repository's recent git activity into a short standup report.

## Steps

1. Run this exact command from the repo root to read recent history:

   ```
   git log --since="7 days ago" --date=short --pretty=format:"%h %ad %an %s" --stat
   ```

   If it prints nothing, fall back to `git log -n 10 --date=short --pretty=format:"%h %ad %an %s"`.

2. Write the report in Markdown using exactly this structure. The first line
   must be exactly `# Standup`, and the three section headings must be spelled
   exactly as shown:

   ```
   # Standup

   ## Yesterday
   - <what was actually done, one bullet per meaningful commit or group of commits>

   ## Today
   - <logical next steps inferred from the recent work>

   ## Blockers
   - <anything stuck, risky, or unfinished; write "None" if nothing applies>
   ```

## Rules

- Base `## Yesterday` only on real commits from the git output. Mention the
  actual files or behaviors changed (for example, `parser.py` handling of
  empty lines). Never use generic filler.
- Keep each bullet to one line.
- Do not output anything before `# Standup`.
- If asked to save the report, write it to `STANDUP.md` in the repo root.
EOF
```

## Part 2: use it

### Option A: console

```bash
cd /root/standup-demo
claude
# then inside the session:
/standup
# approve the write of STANDUP.md when prompted
```

### Option B: headless

```bash
cd /root/standup-demo
{
  cat /root/.claude/skills/standup/SKILL.md
  echo
  echo "=== GIT HISTORY ==="
  git log --since="7 days ago" --date=short --pretty=format:"%h %ad %an %s" --stat
} | claude -p "Follow the standup skill instructions above using the git history provided. Output only the report, starting with '# Standup'." > STANDUP.md
```

### Verify

```bash
head -1 STANDUP.md          # must be exactly: # Standup
grep -E '^## (Yesterday|Today|Blockers)$' STANDUP.md
cat STANDUP.md              # check Yesterday matches the real commits
```

## Expected `STANDUP.md`

Your agent's wording will differ slightly, but `## Yesterday` should match these three commits:

```markdown
# Standup

## Yesterday
- Added a CSV parser in `parser.py` that splits a line on commas (21f3abb).
- Fixed a crash on empty input: `parse()` now returns `[]` for blank lines (683f4a8).
- Added a README describing the standup-demo parsing utility (b601bcc).

## Today
- Add tests for `parse()` covering empty, whitespace-only, and normal CSV lines.
- Handle quoted fields and trailing commas in the CSV parser.
- Expand the README with usage examples.

## Blockers
- None.
```