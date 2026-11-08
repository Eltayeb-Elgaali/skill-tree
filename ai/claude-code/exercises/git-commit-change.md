# Task: Let Claude Commit Your Change

## Goal

Set up a small git repo, make a change, and have Claude stage it and write a commit message from the diff.

## Setup

Create a repo with one tracked file, then edit it. Paste this block:

```bash
mkdir -p /root/gitproj
cd /root/gitproj
git init
echo "# Notes" > notes.md
git add notes.md
git commit -m "initial commit"
echo "- remember to review diffs before committing" >> notes.md
```

If git asks who you are, set an identity once (Claude can also do this for you if you ask):

```bash
git config user.email "you@example.com"
git config user.name "Lab User"
```

Now `notes.md` has an uncommitted change waiting.

## Practice

Open the console in the repo and ask Claude to commit:

```bash
cd /root/gitproj && claude
```

Then give it this prompt:

```
Stage the change to notes.md and commit it with a clear message based on the diff.
```

Claude will read the diff, stage the file, and write a message describing what changed. Approve the commands it runs, `/exit`, and check the log:

```bash
git -C /root/gitproj log --oneline
```

## Expected Result

`/root/gitproj` is a git repo with at least one commit beyond the setup: the one Claude made, with a message that reflects the line you added to `notes.md`.