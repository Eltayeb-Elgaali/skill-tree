# Task: Prove a Cross-Root Read

## The idea in one minute

Claude Code can only work inside the **directory you launch it from** (its "working root"). To let it read a sibling directory, you must explicitly grant access, either for one run or permanently.

| Directory | Role | Status |
|---|---|---|
| `/root/roots/svc-api` | Working root (holds `main.py`) | Allowed by default |
| `/root/roots/svc-schema` | Sibling service (holds `schema.json`) | Locked until you grant it |
| `/root/roots/private-notes` | Out of scope | **Never grant, never write** |

## What you must prove

1. **Without a grant**, Claude cannot read `schema.json` and replies `NO ACCESS`.
2. **With a one-run grant** (`--add-dir`), Claude reads it and returns the `contract` value.
3. **With a permanent grant** (`settings.json`), the permission is saved for future runs.

## Deliverables

All four go in `/root/roots/svc-api`:

- [ ] `denied.txt`: output of the ungranted run (stdout and stderr)
- [ ] `granted.txt`: output of the granted run, containing the contract value
- [ ] `.claude/settings.json`: valid JSON with `additionalDirectories`
- [ ] `roots.md`: short report starting with `ROOTS REPORT`

## Steps

> [!IMPORTANT]
> Do the denied run **before** creating `settings.json`. Otherwise the grant is already active and you won't get a denial.

### 1. Start in the working root

```bash
cd /root/roots/svc-api
```

### 2. Ungranted run

```bash
claude -p "What is the value of the \"contract\" field in /root/roots/svc-schema/schema.json? If you cannot read the file, reply with the exact line NO ACCESS." > denied.txt 2>&1
```

`2>&1` captures standard error too, as the task requires.

### 3. Granted run

```bash
claude -p "What is the value of the \"contract\" field in /root/roots/svc-schema/schema.json?" --add-dir /root/roots/svc-schema > granted.txt 2>&1
```

### 4. Permanent grant

```bash
mkdir -p .claude
cat > .claude/settings.json <<'EOF'
{
  "permissions": {
    "additionalDirectories": ["/root/roots/svc-schema"]
  }
}
EOF
```

The array must contain exactly that one path.

### 5. Write the report

```bash
cat > roots.md <<'EOF'
ROOTS REPORT
Working root: /root/roots/svc-api
Added root: /root/roots/svc-schema
Never granted: /root/roots/private-notes
EOF
```

The first line must be exactly `ROOTS REPORT`.

### 6. Verify, then submit

```bash
cat denied.txt                               # should say NO ACCESS
cat granted.txt                              # should show the contract value
python3 -m json.tool .claude/settings.json   # confirms valid JSON
cat roots.md
```

Then press **Submit**.

## Common pitfalls

| Pitfall | Why it matters |
|---|---|
| Writing `settings.json` too early | The denied run would succeed instead of failing |
| Forgetting `2>&1` | Standard error wouldn't be captured |
| Launching from the wrong directory | The working root is wherever you start |
| Extra paths in `additionalDirectories` | It must hold only `/root/roots/svc-schema` |
| Touching `private-notes` | Its single line is checked at the end |