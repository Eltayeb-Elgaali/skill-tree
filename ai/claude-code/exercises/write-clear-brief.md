## Goal

Practice the whole loop: write a clear task brief, hand it to Claude, and run what comes back. A good brief names a goal, a target file, and a done-condition.

### What the check expects

Three files:

- /root/brief.md - your task brief, not empty
- /root/greet.sh - the script Claude builds, executable
- /root/greet.out - the output from running it, not empty

### Practice

First write the brief. Keep it short and concrete:

```bash
cat > /root/brief.md <<'EOF'
Goal: a greeting script for our servers.
Target file: /root/greet.sh
Done when: running it prints "Hello from <hostname>" using the real hostname,
and it exits 0.
EOF
```

Now hand the brief to Claude and let it build the script. Allow the edit so it can write the file:

```bash
claude -p --permission-mode acceptEdits "Build the script described in /root/brief.md"
```

Then do your half - make it executable and run it:

```bash
chmod +x /root/greet.sh
/root/greet.sh > /root/greet.out
cat /root/greet.out
```

If the result is not quite right, tell Claude what to fix rather than rewriting from scratch.

### Expected Result

A brief that Claude turned into a working script, which you made executable and ran to produce output.