# Subagents in Claude Code

When a job is big or self-contained, Claude can hand it to a **subagent**: a separate helper that runs the task in its own context and reports back just the result. Claude does this through the **Task tool**. The main conversation stays focused, and the subagent's noisy intermediate steps never clutter your thread.

## Why Delegate

- **Keep the main context clean.** A subagent that reads twenty files to answer one question returns only the answer. The twenty files never enter your main conversation.
- **Run independent work in parallel.** Several subagents can work at once on jobs that don't depend on each other, then report back.

Good candidates are a big search across a codebase, or a well-bounded task like "review this directory and summarize what each file does."

## Custom Agent Types

Beyond the built-in general agent, you can define your own agent types. Each is a markdown file at `.claude/agents/<name>.md` with YAML frontmatter and a body that describes the agent's job:

```markdown
---
name: reviewer
description: Reviews code changes for bugs and risky edits
tools: Read, Grep, Bash
---
You are a code reviewer. Read the changed files, look for bugs, missing
error handling, and anything risky to ship. Report findings as a short list.
```

| Frontmatter key | What it does |
|-----------------|--------------|
| `name` | The agent type's identifier |
| `description` | When Claude should reach for this agent |
| `tools` | Which tools the agent may use (optional) |

The body is the agent's **system prompt**: its standing instructions.

## Managing Agents

Run `/agents` in the console to list, create, and edit agent types. Once an agent exists and its description fits the job, Claude can delegate to it on its own, or you can ask for it by name.

## Next Step

Define a `reviewer` agent of your own.