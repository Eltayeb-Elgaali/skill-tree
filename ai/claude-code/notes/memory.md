# Rules vs. memories, in simple words

The text says there are two kinds of notes you can keep, and the only difference between them is **what they say**, not **how they're stored**.

## The two kinds

**Rules** tell Claude *how to behave*.
Example: "Always use python3."
This changes what Claude does.

**Memories** tell Claude *facts to know*.
Examples: "The deploy command is X." "Staging lives at Y."
This doesn't change behavior. It just gives Claude information it would otherwise have to guess or ask about.

## What "content, not mechanism" means

*Mechanism* is how something works behind the scenes. *Content* is what is written in it.

Here, the mechanism is the same for both: you write a line in a CLAUDE.md file. Nothing special happens technically. The only difference is whether the line is an instruction ("do this") or a fact ("this is true").

## Where they live and how long they last

Both go in the **same files** (CLAUDE.md), and both **survive**:

- `/clear`: wipes the current conversation
- `/compact`: shrinks the conversation into a summary
- a **reboot**: restarting your computer

That's because they're saved in a file on disk, not held in the conversation. When a new session starts, Claude reads the file again, so the notes are always there.

## Quick summary

| | Rule | Memory |
|---|---|---|
| What it is | An instruction | A fact |
| Example | "Always use python3" | "Staging lives at Y" |
| Effect | Shapes how Claude acts | Gives Claude information |
| Where saved | CLAUDE.md | CLAUDE.md |
| Survives `/clear`, `/compact`, reboot | Yes | Yes |