# Task: Run a multi-tool task and capture the whole run

The folder `/root/loop-lab/inventory` holds three warehouse files: `north.txt`, `south.txt`, and `east.txt`. Reading all three takes the agent several tool calls, which makes this your first genuinely multi-turn run.

Produce two artifacts with one headless run started from `/root/loop-lab`:

- `/root/loop-lab/run.json`: the JSON envelope of the run. Ask the agent to read the three inventory files and report on them, using the output format that produces the envelope, redirected into this file.
- `/root/loop-lab/summary.md`: the reply text extracted from `run.json`. It must begin with the exact line `INVENTORY SUMMARY` and must name all three warehouses (north, south, and east), each with its item count.

> **Tip:** Any model works; the fast alias keeps the wait short.

> **Tip:** Verify the `num_turns` field inside `run.json`. That number is the loop count.


## Step 1: Go to the working folder and check the inputs

```bash
cd /root/loop-lab
ls inventory
```

You should see `north.txt`, `south.txt`, and `east.txt`.

## Step 2: Run the headless prompt and capture the JSON envelope

```bash
claude -p "Read all three files in inventory/ (north.txt, south.txt, east.txt) and report on them. Begin your reply with the exact line INVENTORY SUMMARY, then name each warehouse (north, south, east) with its item count." \
  --output-format json \
  --model haiku \
  --allowedTools "Read" > run.json
```

> **Note:** `--allowedTools "Read"` lets the agent read the files without a permission prompt, and `--model haiku` keeps the wait short. If the files are not found, add `Glob` and `Bash(ls:*)` to the allowed tools.

## Step 3: Extract the reply text into `summary.md`

```bash
jq -r '.result' run.json > summary.md
```

## Step 4: Check the summary

```bash
cat summary.md
head -n 1 summary.md
```

The first line must be exactly `INVENTORY SUMMARY`, and the text must mention north, south, and east, each with an item count.

## Step 5: Look at the number of turns

```bash
jq '.num_turns' run.json
```

Reading three files takes several tool calls, so expect a number above 1. That count is the agent loop, measured.

## Step 6: Verify both artifacts exist

```bash
ls -l /root/loop-lab/run.json /root/loop-lab/summary.md
```

Both files should be non-empty.

## Troubleshooting

- **First line is not `INVENTORY SUMMARY`:** rerun Steps 2 and 3 with a stricter prompt, for example `"Your first line must be exactly INVENTORY SUMMARY, with nothing before it."`
- **`summary.md` is empty or says `null`:** open `run.json` and check the `result` and `is_error` fields for the actual error.
- **Item counts look wrong:** run `wc -l inventory/*.txt` to compare, since "item count" may mean lines or entries depending on the file format.
- **Authentication error:** run `claude` once interactively to log in, then retry.