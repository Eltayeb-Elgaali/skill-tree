```bash
claude -p --model haiku  --output-format json "$PROMPT" > haiku.json
claude -p --model sonnet --output-format json "$PROMPT" > sonnet.json
```