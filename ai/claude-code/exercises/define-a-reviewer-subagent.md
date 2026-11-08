# Task: Define a `reviewer` Subagent

## Goal

Create a custom agent type named `reviewer` in the `agentproj` project, so Claude can hand code-review work to a helper that runs in its own context.

## Practice

A custom agent is a markdown file with YAML frontmatter plus a body. Make the directory first:

```bash
mkdir -p /root/agentproj/.claude/agents
```

Then write `reviewer.md`. It needs at least a `name` and a `description` in the frontmatter, and a body describing the agent's job:

```bash
cat > /root/agentproj/.claude/agents/reviewer.md <<'EOF'
---
name: reviewer
description: Reviews code changes for bugs and risky edits
tools: Read, Grep, Bash
---
You are a code reviewer. Read the changed files, look for bugs, missing
error handling, and anything risky to ship. Report findings as a short list.
EOF
```

The body is the agent's standing instructions. Make it specific to the job you want done.

## Expected Result

A non-empty `reviewer.md` under `/root/agentproj/.claude/agents/` whose frontmatter includes both `name` and `description`. Run `/agents` in the console and it appears in the list.