# Task: Run your first agent task

Prove the whole stack works by leaving three artifacts in a new folder.

## Files to create

- `/root/first-contact/hello.py`: a small Python script you write yourself, containing at least one `print` statement.
- `/root/first-contact/version.txt`: the output of the command that prints the installed Claude Code version.
- `/root/first-contact/report.txt`: the agent's reply to a headless prompt. Ask Claude to read `hello.py` and report what it does, and tell it to begin the reply with the exact line `REPO REPORT`. Redirect the reply into the file.

> **Tip:** Any model works for the report. If it feels slow, ask for the fast one with `--model` and the alias `haiku`.


# Run your first agent task: step by step

## Step 1: Create the folder

```bash
mkdir -p /root/first-contact
cd /root/first-contact
```

## Step 2: Write `hello.py`

```bash
cat > hello.py << 'EOF'
name = "Claude"
print(f"Hello, {name}!")
print("The stack works.")
EOF
```

Optionally confirm it works:

```bash
python3 hello.py
```

## Step 3: Save the Claude Code version

```bash
claude --version > version.txt
cat version.txt
```

## Step 4: Run the headless prompt and save the reply

```bash
claude -p "Read hello.py and report what it does. Begin your reply with the exact line REPO REPORT" --model haiku > report.txt
```

> **Note:** `--model haiku` is optional, but it makes the run faster. If Claude asks for permission to read the file and the run stalls or fails, add `--allowedTools "Read"` to the command.

## Step 5: Check the report

```bash
cat report.txt
head -n 1 report.txt
```

The first line must be exactly `REPO REPORT`.

## Step 6: Verify all three files exist

```bash
ls -l /root/first-contact
```

You should see `hello.py`, `version.txt`, and `report.txt`, and `report.txt` should not be empty.

## Troubleshooting

- **First line is not `REPO REPORT`:** rerun Step 4 with a stricter prompt, for example `"Your first line must be exactly REPO REPORT, with nothing before it."`
- **Empty `report.txt`:** run the `claude -p ...` command without `> report.txt` to see the error.
- **Authentication error:** run `claude` once interactively to log in, then retry.