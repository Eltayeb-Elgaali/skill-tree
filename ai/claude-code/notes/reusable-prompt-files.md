# Reusable Prompt Files (Summary)

Typing the same prompt repeatedly leads to drift (dropped steps, reworded requirements). The fix: **save the prompt in a file**.

## What a prompt file is

* Plain markdown with instructions for one repeated task.
* No frontmatter, no registration, no dependence on bundled skills.
* Works with headless `-p` runs, the console, and other agent CLIs.

## Feeding a file into a headless run

Command substitution `$(...)` inserts a file's contents into the command:

```bash
claude -p "$(cat /root/prompts/summarize.md)

Input:
$(cat data.txt)" > out.txt
```

* Keep the **quotes**: they make the whole text one argument, newlines included.

## The output contract

Make results predictable so you can build on them:

* A **fixed first line** (a script can verify the run worked)
* **Required sections**, named exactly
* An instruction to **name the files examined**

With a contract, `grep` can check every run, and pipelines can use the output without human review.

## From prompt file to skill

A prompt file is the early form of a skill. Add frontmatter and move it into a skills folder, and it can be invoked by name and loaded automatically. The instructions barely change.

## Commands this node introduces

* `$(cat file)` (command substitution) inserts a file's contents into a prompt string.
* `mkdir -p` creates the folder that holds your prompt library.