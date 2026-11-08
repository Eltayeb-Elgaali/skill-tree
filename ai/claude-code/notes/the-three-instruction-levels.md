# CLAUDE.md Levels

## The idea

`CLAUDE.md` is not the only instruction file Claude Code loads. At session start it gathers instructions from up to **three levels** and puts them all in the context window together.

| Level | File | Who it serves |
|---|---|---|
| **Managed** | Admin-controlled policy file | The whole organization |
| **Project** | `CLAUDE.md` in the repo (and parent dirs) | Everyone on this repo |
| **User** | `~/.claude/CLAUDE.md` | You, in every project |

On this VM you are root, so the user-level file is `/root/.claude/CLAUDE.md`.

## What goes where

| Put it in... | What belongs there |
|---|---|
| **User file** | Personal preferences: reply style, shortcuts, how you like explanations pitched |
| **Project file** | Team knowledge. It is committed and shared, so keep personal taste out |

## When levels disagree

All files load, so most rules simply **add up**. A conflict only exists when two levels give opposing orders. Then the more specific level is meant to win:

**Managed policy** > **Project** > **User**

> [!TIP]
> Don't leave the model to referee a contradiction. If a project intentionally overrides a personal preference, say so in the project file, for example: `This project overrides any user-level reply style.` One line removes the ambiguity.

## Creating the user-level file

It is just a file. Create the folder first if it doesn't exist:

```bash
mkdir -p /root/.claude
cat > /root/.claude/CLAUDE.md <<'EOF'
# Personal preferences
- Keep explanations short.
EOF
```

## Commands introduced

None. The user-level file is a plain file you create with an editor or a heredoc.

## The lab

The lab stages exactly this conflict so you can watch the project file win over the user file.