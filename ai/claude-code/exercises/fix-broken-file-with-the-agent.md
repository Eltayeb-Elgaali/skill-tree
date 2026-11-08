# Task: Fix a Broken File with the Agent

`/root/editor-lab/greeting.py` prints a misspelled greeting. Get Claude Code to repair it and to log what it did.

## Requirements

| File | Requirement |
| --- | --- |
| `/root/editor-lab/greeting.py` | Fixed by the agent so that running it prints `Hello world` with correct spelling and a capital H. |
| `/root/editor-lab/CHANGES.md` | Created by the agent. It must begin with the exact line `EDIT LOG`, name `greeting.py`, and describe the fix in a sentence. |

## Approaches

Both paths from this topic's concepts work:

1. **Interactive console:** open Claude Code in `/root/editor-lab` and approve the edit prompts as they appear.
2. **Headless:** run with the permission mode that auto-accepts file edits.

## Notes

- The agent needs nothing beyond **Edit** and **Write** for this job, so no Bash rule is required.

