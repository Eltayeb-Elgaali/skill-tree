```bash
claude -p --output-format json "Reply with the single word ready." | jq -r '.num_turns'
```