# Comparing Models with the JSON Envelope

## The idea

A headless run with JSON output prints an **envelope** of numbers. Use it to compare models with data instead of gut feeling.

## Three useful fields

| Field | Meaning |
|---|---|
| `duration_ms` | How long the run took, end to end |
| `total_cost_usd` | What the run cost in tokens |
| `num_turns` | How many loop iterations the task needed |

Extract them from a saved envelope with `jq`:

```bash
jq '.duration_ms, .total_cost_usd' haiku.json
```

## Making the comparison fair

Change **one variable only**: the model.

- [x] Same prompt, word for word
- [x] Same working folder
- [x] Only the `--model` flag differs
- [x] Save both envelopes as evidence

## What the numbers cannot tell you

> [!WARNING]
> The envelope measures speed and cost, **not answer quality**. Read both results yourself.

- A cheap wrong answer loses to an expensive right one.
- Often the cheap answer is just as right.

## Beware of noise

A single run is a noisy