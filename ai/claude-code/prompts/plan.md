```bash
claude -p --permission-mode plan "Outline the steps to add config backups to /root/tool.sh. Do not change any files."
```

```bash
claude -p --permission-mode plan \
  "Plan adding a --verbose flag to a shell script. List the steps." \
  > /root/plan.md
  ```
  ```bash
  claude -p --permission-mode plan "How should app.py be split into modules?"
  ```
 ```bash
claude -p --permission-mode plan "Plan the change" > plan.md
claude -p --permission-mode acceptEdits "Implement the plan in plan.md"
 ```