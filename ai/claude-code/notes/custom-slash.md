# Custom Slash Commands in Claude Code

You already use built-in slash commands like `/init` and `/model`. You can add your own. A custom slash command is just a prompt you type often, saved to a file so you can fire it with a few keystrokes instead of retyping it.

## Where They Live

A custom command is a markdown file. Drop it in one of two places:

| Location | Scope |
|----------|-------|
| `.claude/commands/<name>.md` | **Project**: shared with anyone who clones the repo |
| `~/.claude/commands/<name>.md` | **User**: available in every project you open |

The file name becomes the command name. `.claude/commands/review.md` gives you `/review` in the console.

## The File Body Is the Prompt

There is no special language here. Whatever you write in the body is the prompt Claude receives when you run the command. A `review.md` might contain:

```markdown
Review the staged changes with `git diff --staged` and flag any bugs,
missing error handling, or secrets that would be risky to commit.
```

Now typing `/review` sends exactly that prompt.

## Passing Arguments

Use `$ARGUMENTS` and whatever you type after the command name gets substituted in. A `fix.md` containing:

```markdown
Find and fix the bug described here: $ARGUMENTS
```

lets you run `/fix the login form rejects valid emails`, and that text lands where `$ARGUMENTS` sits.

## Optional Frontmatter

A YAML frontmatter block at the top can set:

- `description`: shown in the command list
- `allowed-tools`: tools pre-approved for this invocation

```markdown
---
description: Review staged git changes
allowed-tools: Bash(git diff:*)
---
Review the staged changes and flag issues.
```

`allowed-tools` grants permission; it does not remove other tools. Your permission settings still govern actions outside this grant.

Frontmatter is optional. A plain body with no frontmatter works fine.

## Next Step

Write your own command and confirm the file lands in the right place.