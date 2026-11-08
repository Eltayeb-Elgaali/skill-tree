# Claude Code Skills

A **skill** is a folder named after the skill, containing a `SKILL.md` file with two parts: YAML frontmatter (between `---` lines) and markdown instructions.

## Example

```markdown
---
name: release-notes
description: Draft release notes from recent commits. Use when asked
  for release notes or a changelog for this repo.
---

Look at the recent git history. Group the changes into Features,
Fixes, and Internal. Write the result as markdown with those three
headings. Keep each entry to one line.
```

## Frontmatter fields

- **`name`**: Identifies the skill and should match the folder name. It becomes the slash command (e.g. `/release-notes`).
- **`description`**: Says what the skill does and when to use it. Claude Code reads this to decide whether to load the skill, so it is the most important line.

## Body

Everything after the frontmatter is the instructions the agent follows when the skill loads. Write it like a brief to a competent colleague: what to look at, what to produce, and what the output must contain.

## Where the folder goes

| Location | Scope |
|---|---|
| `~/.claude/skills/<name>/SKILL.md` | You, in every project |
| `.claude/skills/<name>/SKILL.md` | Everyone using this repo |

## How loading works

1. Your request is checked: does it match a skill's `description`, or did you type `/name`?
2. **Yes** → `SKILL.md` loads and the agent follows its instructions.
3. **No** → the skill stays unloaded.

## Key property

Skills load **on demand**, so an unloaded skill costs nothing. A library of twenty skills is fine, whereas twenty pages of `CLAUDE.md` would drown every session.

## Commands

No new commands. Create a skill with `mkdir -p` and a text editor or heredoc, then invoke it as `/name`.