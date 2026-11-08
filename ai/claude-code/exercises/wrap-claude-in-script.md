# Task: Wrap Claude in a script

## Goal

Write an executable script at `/root/auto/classify.sh` that takes a line of text, asks Claude to classify it, and saves the answer to `/root/auto/out.txt`. Then run it so the output file appears.

## Practice

Make the working directory:

```bash
mkdir -p /root/auto
```

A minimal script takes the input as an argument, calls headless Claude with JSON output, and writes the parsed result:

```bash
#!/usr/bin/env bash
LINE="$1"
claude -p "Classify this line as INFO or ERROR, reply with one word: $LINE" \
  --output-format json | jq -r '.result' > /root/auto/out.txt
```

Save that as `/root/auto/classify.sh`, make it executable, and run it with a sample line:

```bash
chmod +x /root/auto/classify.sh
/root/auto/classify.sh "disk full on server node-3"
cat /root/auto/out.txt
```

## Expected Result

`/root/auto/classify.sh` is executable, and running it leaves a non-empty `/root/auto/out.txt` with Claude's classification.