```bash
Stage the change to notes.md and commit it with a clear message based on the diff.
```
```bash
cd /root/webapp
claude -p "Look at the uncommitted change in this repository. Reply with exactly one line and nothing else, a conventional commit message for it starting with fix:" > /root/commit-message.txt
```
```bash
git commit -F /root/commit-message.txt
```