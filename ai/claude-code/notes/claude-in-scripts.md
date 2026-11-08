# Headless mode

Headless mode is what makes Claude Code scriptable. A single command like

```bash
claude -p "Summarize this log in one line" --output-format json
```

runs Claude without an interactive session, prints a result, and exits. That makes it a normal building block you can drop into a shell script, a cron job, or a CI pipeline.

## Parse the output

With `--output-format json` the reply comes back as structured JSON, so you can pull out exactly the field you want with `jq`:

```bash
claude -p "Classify this line as INFO or ERROR: $LINE" \
  --output-format json | jq -r '.result'
```

Now the answer is a plain string your script can act on: write it to a file, branch on it, or feed it to the next step.

## Rules for unattended runs

When nothing is watching the terminal, a few habits keep automation safe and predictable:

- **Grant permissions up front.** A scheduled run cannot answer a permission prompt, so decide what it may do before it starts. Use a scoped allowlist in settings, or a permission mode like `acceptEdits` when it needs to write files. Reach for `bypassPermissions` only in a throwaway sandbox, never against anything real.
- **Keep prompts tight.** One clear task per call. Say what output you want and in what shape.
- **Log what it did.** Redirect output to a file with a timestamp so you can see later what ran and what it decided.

## It does not wait for you

The point of headless mode is that it runs non-interactively. Nothing pauses for a human to type `y`. That is exactly what you want for automation, and also exactly why you scope its permissions carefully before you schedule it.