## Goal

By default claude -p prints just the answer text. With --output-format json it prints a structured record instead: the answer plus metadata like token usage. That machine-readable shape is what makes Claude Code scriptable.

Produce both forms under /root/dns/:

/root/dns/answer.json - the full JSON reply to a short question
/root/dns/answer.txt - just the answer text, pulled out of that JSON

## Practice

Ask a small question in JSON mode, then read one field out of it with jq:

```bash
mkdir -p /root/dns
claude -p "Explain what DNS is in one sentence." --output-format json > /root/dns/answer.json
jq -r '.result' /root/dns/answer.json > /root/dns/answer.txt
```

The result field holds the model's answer. Other fields in the same object report things like token usage, which is handy when a script needs to log cost or check length.

## Expected Result

A valid JSON file that jq can parse, and a plain-text file containing only the one-sentence answer.

