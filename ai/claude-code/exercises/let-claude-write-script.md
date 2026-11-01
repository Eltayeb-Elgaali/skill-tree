## Goal

Have Claude Code create a working script, then run it yourself - the basic division of labour when you work with a coding agent.

### Practice

Headless Claude may not touch files unless you allow it. Grant edits up front:

```bash
claude -p --permission-mode acceptEdits \
  "Create a bash script at /root/sysinfo.sh that prints the hostname, kernel version, and free disk space, each labeled."
```

### Then do your half:

```bash
chmod +x /root/sysinfo.sh
/root/sysinfo.sh > /root/sysinfo.out
cat /root/sysinfo.out
```

### Expected Result

A labeled system report produced by a script an AI wrote - which you reviewed, made executable, and ran. You stayed in charge.