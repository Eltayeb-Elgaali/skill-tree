# Claude Code Foundations: Summary

Seventeen hours of foundations reduce to three groups of ideas. The challenge that follows tests whether you can apply them without hints.

## Driving the agent

| Topic | Takeaway |
|---|---|
| Agent loop | Reads, acts with tools, checks, and repeats until done |
| Tools and permissions | Reads are free; edits and bash ask; deny rules always win |
| Models | haiku for speed, sonnet for balance, opus for depth; pick per task |
| Context | The window fills; `/compact` summarizes so long sessions keep working |
| Headless mode | `-p` turns any task into a shell command with redirectable stdout |

## Instructing the agent

| Topic | Takeaway |
|---|---|
| CLAUDE.md | Project instructions loaded every session; `/init` drafts them |
| Precedence | Project instructions layer over global ones; closest file wins |
| Memory | Preferences persist across sessions without re-explaining |
| Skills | `SKILL.md` packages a capability the agent loads when relevant |
| Slash commands | A markdown file under `.claude/commands` is a reusable prompt |
| Plugins | Commands, skills, and hooks bundle into one installable directory |

## Keeping the agent safe

| Topic | Takeaway |
|---|---|
| Plan mode | Read-only analysis first; edits only after you approve |
| Checkpoints | `/rewind` undoes file edits in-session; bash side effects stay |
| Git workflow | Commit baselines, stage deliberately, let the agent draft conventional messages |

## The challenge ahead

The next node is a challenge with no hints and no walkthrough. You must build a small project end to end:

- A git repository
- A `CLAUDE.md`
- A project slash command
- A headless run of that command
- A clean conventional commit

Everything needed appeared in hours 001 through 017. If a step feels foggy, revisit the matching topic first, because the challenge won't remind you.