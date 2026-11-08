# Skills in Claude Code

A **skill** is a packaged set of instructions for one kind of task, such as a review checklist, a release routine, or a debugging recipe. On disk, it is a folder containing a `SKILL.md` file (frontmatter + instructions):

```text
.claude/skills/
  release-notes/
    SKILL.md
```

## Where skills live

| Location | Scope |
|---|---|
| `~/.claude/skills/` | Personal skills, available in every project |
| `.claude/skills/` (inside a repo) | Shared skills for the whole team |

## Skills vs. CLAUDE.md

* `CLAUDE.md` loads at **every session start**, so it costs tokens every time.
* A skill loads **on demand**, only when it is relevant.
* Use skills for anything **long or task-specific**, like a 30-line review checklist.

## How skills get used

* **Explicitly:** type the skill's name as a slash command. A skill named `standup` runs as `/standup`.
* **Automatically:** when your request matches the skill's description, Claude Code loads it on its own.

## Bundled skills

* Some Claude Code builds ship with built-in skills (for reviewing, simplifying, or debugging code).
* Which ones you get varies by version, so check locally with `/help` instead of trusting online lists.
* Durable workflows should not depend on a bundled skill. Instead, save reusable instructions in a file you control. This works on every build and even with other agent CLIs.

## Commands this node introduces

* `/help` lists the slash commands and skills available in your build.
* A skill named `x` is invoked in the console as `/x`.