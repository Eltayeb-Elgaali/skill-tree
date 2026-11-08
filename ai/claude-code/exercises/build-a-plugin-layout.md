# Task: Build a plugin layout

Build the directory layout for a plugin named `review-helper` under `/root/review-plugin`, exactly as an installable plugin would ship it. No install step is involved; the layout itself is what is graded.

## Requirements

- `/root/review-plugin/.claude-plugin/plugin.json`: a JSON manifest whose `name` field is exactly `review-helper`, with non-empty `description` and `version` fields.
- `/root/review-plugin/commands/checklist.md`: a plugin command in the slash command format, with a `description` field in its frontmatter. The prompt content is up to you.
- `/root/review-plugin/skills/standup/SKILL.md`: a skill file whose frontmatter carries `name` and `description` lines. The instructions below the frontmatter are up to you.

---

# Solution

## Target layout

```
/root/review-plugin/
  .claude-plugin/
    plugin.json
  commands/
    checklist.md
  skills/
    standup/
      SKILL.md
```

## Step 1: Create the directories

The manifest folder is `.claude-plugin` (with the leading dot). `commands` and `skills` sit at the plugin root, not inside `.claude-plugin`.

```bash
mkdir -p /root/review-plugin/.claude-plugin
mkdir -p /root/review-plugin/commands
mkdir -p /root/review-plugin/skills/standup
```

## Step 2: Write the manifest

```bash
cat > /root/review-plugin/.claude-plugin/plugin.json << 'EOF'
{
  "name": "review-helper",
  "description": "Commands and skills for code review chores",
  "version": "0.1.0"
}
EOF
```

## Step 3: Write the command

The file name `checklist.md` becomes the command name `/checklist`. It needs YAML frontmatter with a `description`.

```bash
cat > /root/review-plugin/commands/checklist.md << 'EOF'
---
description: Produce a code review checklist for the current changes
---

Review the current changes in this repository (use `git diff` and `git status`).

Then produce a checklist in Markdown with one `- [ ]` item for each of these areas:

- Correctness: does the code do what it claims?
- Tests: are new behaviors covered?
- Error handling: are edge cases such as empty input handled?
- Readability: are names and structure clear?
- Security: are there secrets or unsafe inputs?

Mention the specific files and functions involved in each item.
EOF
```

## Step 4: Write the skill

The `name` line should match the folder name (`standup`).

```bash
cat > /root/review-plugin/skills/standup/SKILL.md << 'EOF'
---
name: standup
description: Generate a daily standup report from recent git history. Use when the user asks for a standup, daily update, or summary of recent work.
---

# Standup skill

1. Run this command to read recent history:

   ```
   git log --since="7 days ago" --date=short --pretty=format:"%h %ad %an %s" --stat
   ```

2. Write a report that starts with the exact line `# Standup` and has the sections `## Yesterday`, `## Today`, and `## Blockers`.

3. Base `## Yesterday` only on real commits. Never use generic filler.
EOF
```

## Step 5: Verify

```bash
# Show the tree
find /root/review-plugin -type f | sort

# Manifest is valid JSON with the right name
python3 -c "
import json
d = json.load(open('/root/review-plugin/.claude-plugin/plugin.json'))
assert d['name'] == 'review-helper'
assert d['description'] and d['version']
print('manifest OK:', d)
"

# Frontmatter present in command and skill
head -4 /root/review-plugin/commands/checklist.md
head -4 /root/review-plugin/skills/standup/SKILL.md
```

Expected `find` output:

```
/root/review-plugin/.claude-plugin/plugin.json
/root/review-plugin/commands/checklist.md
/root/review-plugin/skills/standup/SKILL.md
```

## Checklist

- [ ] `.claude-plugin/plugin.json` is valid JSON with `"name": "review-helper"`
- [ ] `description` and `version` are non-empty
- [ ] `commands/checklist.md` has frontmatter with `description:`
- [ ] `skills/standup/SKILL.md` has frontmatter with `name:` and `description:`
- [ ] `commands/` and `skills/` are at the plugin root, not inside `.claude-plugin/`