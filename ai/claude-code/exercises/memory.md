## Goal

Create a tiny project, then have the console's /init command analyze it and write CLAUDE.md - the file Claude reads at the start of every future session in that directory.

### Practice

First, a project worth documenting:

```bash
mkdir -p /root/myapp && cd /root/myapp
printf '#!/bin/bash\necho "Hello from myapp"\n' > run.sh
printf 'A tiny demo app for the Claude Code lab.\n' > README.md
```

```bash
claude
```

```bash
/init
```
```bash
cat /root/myapp/CLAUDE.md
```
