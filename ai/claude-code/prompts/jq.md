```bash
jq '.duration_ms, .total_cost_usd, .num_turns' haiku.json
jq '.duration_ms, .total_cost_usd, .num_turns' sonnet.json

jq -r '.result' haiku.json
jq -r '.result' sonnet.json
```