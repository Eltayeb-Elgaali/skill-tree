# The Context Window

## The idea

Every session runs inside a **context window**: the fixed amount of text the model can see at once, measured in **tokens** (one token is roughly three quarters of an English word).

The window holds:

- The system prompt
- Your messages
- Every file the agent reads
- The output of every command it runs
- The agent's own replies

Nothing the model "knows" about your session lives anywhere else. The window fills up, and a long session can burn most of its budget on history you no longer need.

## Inside the window vs on disk

| Lives inside the window (temporary, limited) | Lives on disk (durable, unlimited) |
|---|---|
| The conversation so far | Files the agent created or edited |
| File contents the agent read | Git history and commits |
| Command output from tools | Project instruction files |
| The agent's own replies | Anything you saved deliberately |

> [!TIP]
> When something matters beyond the current conversation, get it onto disk.

## What a full window costs you

| Problem | What happens |
|---|---|
| **Recall degrades** | Early details get summarized away or compete with newer text for attention |
| **Every turn costs more** | The whole window is re-sent each turn, so bloated sessions are slower and pricier |
| **Compaction kicks in** | Near the limit, Claude Code compresses the conversation automatically (covered in the next node) |

## Watching and controlling usage

Two habits keep the window healthy:

1. **Scope each session to one task.** Don't mix a bug hunt, a refactor, and a docs rewrite in one conversation.
2. **Run `/clear` between unrelated tasks.** It wipes the conversation for a fresh window, and everything on disk stays put.

## Commands introduced

| Command | What it does |
|---|---|
| `/context` | Shows how full the window is and what is taking up space |
| `/clear` | Resets the conversation without touching your files |