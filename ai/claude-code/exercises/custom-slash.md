# Task: Create a `/review` Command

## Goal

Save a prompt you would type often as a reusable slash command in the `cmdproj` project, so it becomes `/review` in the console.

## Practice

A custom command is a markdown file whose body is the prompt. Create the directory and the file:

```bash
mkdir -p /root/cmdproj/.claude/commands
```

Then write the prompt into `review.md`. You can do this in an editor, have Claude write it, or just redirect text:

```bash
cat > /root/cmdproj/.claude/commands/review.md <<'EOF'
Review the staged changes with `git diff --staged` and flag any bugs,
missing error handling, or risky changes. Focus on: $ARGUMENTS
EOF
```

The `$ARGUMENTS` line is optional. Use it if you want to pass a focus area when you run the command. A plain prompt with no `$ARGUMENTS` is fine too.

## Expected Result

A non-empty `review.md` under `/root/cmdproj/.claude/commands/`. Launch `claude` from `/root/cmdproj` and it shows up as `/review`.