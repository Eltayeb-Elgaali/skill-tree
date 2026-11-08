```bash
In /root/deploy.sh, the rsync command drops the trailing slash on $SRC so it
copies the folder instead of its contents. Fix that line and leave the rest
unchanged.
```

```bash
Running tally.sh gives: "line 5: DIR: unbound variable". Fix it so it runs cleanly.
```

```bash
Run the test and, if it fails, fix the cause and run it again until it passes.
```

```bash
Running tally.sh fails with "DIR: unbound variable". Read the script, fix the cause, and run it again so it prints the file count cleanly.
```