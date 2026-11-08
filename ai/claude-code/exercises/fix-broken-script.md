# Task: Have Claude Fix a Broken Script

## Goal

You have a small bash script that is supposed to count the files in a folder and print the total, but it errors the moment you run it. Show Claude the failure and let it find and fix the cause.

## Setup

Paste this whole block. It creates a folder with a few files and writes a deliberately broken `tally.sh`:

```bash
mkdir -p /root/bug/data
touch /root/bug/data/one.log /root/bug/data/two.log /root/bug/data/three.log
cat > /root/bug/tally.sh <<'EOF'
#!/bin/bash
set -euo pipefail
# tally.sh - count the files in data/ and print the total
TARGET_DIR="/root/bug/data"
count=$(ls "$DIR" | wc -l)
echo "Total files: $count"
EOF
```

Run it once to see it break:

```bash
bash /root/bug/tally.sh
```

You will get an error like `DIR: unbound variable`. The script sets `TARGET_DIR` but then reads `$DIR`, a name that was never defined.

## Practice

Open the console and hand Claude the exact error:

```bash
cd /root/bug && claude
```

Then give it this prompt:

```
Running tally.sh fails with "DIR: unbound variable". Read the script, fix the cause, and run it again so it prints the file count cleanly.
```

Let Claude locate the mismatched variable, correct it, and rerun. Approve the edit, `/exit`, and confirm it works:

```bash
bash /root/bug/tally.sh
```

## Expected Result

`tally.sh` runs without errors and prints a line like `Total files: 3`. The check runs the script, captures its output, and confirms it exits cleanly with a number in the output.