# Task: Run One Task on Two Models

## Goal

Run the **identical prompt** on `haiku` and `sonnet`, save both JSON envelopes, then write your own verdict in `comparison.md`.

## Deliverables

All three go in `/root/model-compare/`:

- [ ] `haiku.json`: JSON envelope of the run on the `haiku` alias
- [ ] `sonnet.json`: JSON envelope of the identical prompt on the `sonnet` alias
- [ ] `comparison.md`: written by **you**, not the agent

`comparison.md` must:

1. Begin with the exact line `MODEL COMPARISON`
2. Name both haiku and sonnet
3. Quote the duration each run took
4. State which model you would pick and why

## Steps

### 1. Go to the folder

```bash
mkdir -p /root/model-compare
cd /root/model-compare
```

### 2. Fix one prompt

Store it in a variable so both runs use exactly the same words:

```bash
PROMPT="In two sentences, explain what the -r flag does in the grep command."
```

### 3. Run it on both models

Only `--model` changes. `--output-format json` produces the envelope.

```bash
claude -p --model haiku  --output-format json "$PROMPT" > haiku.json
claude -p --model sonnet --output-format json "$PROMPT" > sonnet.json
```

### 4. Read the numbers and both answers

```bash
jq '.duration_ms, .total_cost_usd, .num_turns' haiku.json
jq '.duration_ms, .total_cost_usd, .num_turns' sonnet.json

jq -r '.result' haiku.json
jq -r '.result' sonnet.json
```

> [!IMPORTANT]
> Read both `result` fields yourself before writing the verdict. The question is not which run was faster, but whether the slower one **bought you anything**.

### 5. Write `comparison.md` yourself

Replace the bracketed parts with your real numbers and reasoning:

```bash
cat > comparison.md <<'EOF'
MODEL COMPARISON

Prompt: In two sentences, explain what the -r flag does in the grep command.

- haiku duration: [X] ms
- sonnet duration: [Y] ms

Verdict: I would pick [haiku/sonnet] for a task like this, because [reason based on
what you read in the two results].
EOF
```

To print the durations for copying in:

```bash
echo "haiku:  $(jq .duration_ms haiku.json) ms"
echo "sonnet: $(jq .duration_ms sonnet.json) ms"
```

### 6. Verify

```bash
ls /root/model-compare                               # haiku.json  sonnet.json  comparison.md
head -1 comparison.md                                # MODEL COMPARISON
python3 -m json.tool haiku.json  > /dev/null && echo ok
python3 -m json.tool sonnet.json > /dev/null && echo ok
```

## Tips

| Situation | What to do |
|---|---|
| Durations are close | Treat it as a tie and rerun once if unsure. Only a gap of about 3x is a real signal. |
| Both answers are equally correct | The cheaper, faster model (`haiku`) is the sensible pick for a simple task. |
| One answer is clearly wrong or shallow | Escalate to `sonnet` and say so in your verdict. |
| Tempted to let the agent write `comparison.md` | Don't. The task requires that you write it. |

## Common pitfalls

| Pitfall | Why it matters |
|---|---|
| Changing the prompt between runs | The comparison is no longer fair |
| Forgetting `--output-format json` | The file won't be a JSON envelope |
| Writing the verdict before reading both results | The task checks that your reasoning reflects the actual answers |
| Wrong first line in `comparison.md` | It must be exactly `MODEL COMPARISON` |