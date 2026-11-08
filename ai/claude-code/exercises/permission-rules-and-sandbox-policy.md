# Project permission rules and sandbox policy

## Task

The project at `/root/guarded` holds a `secrets.env` file that no agent needs to read and a `notes.md` it may work on freely. Give the project a settings file that a whole team could commit: permission rules for what the agent may propose, and a sandbox policy for what a running command can reach.

### Create

`/root/guarded/.claude/settings.json` - valid JSON with a top-level `permissions` object and a top-level `sandbox` object.

### The `permissions` object must contain

- a `deny` array holding this exact rule string:

  ```text
  Bash(rm -rf:*)
  ```

- an `allow` array holding this exact rule string:

  ```text
  Bash(ls:*)
  ```

### The `sandbox` object must

- be enabled, using a JSON boolean
- deny reads of the project's secret file through its filesystem policy, with this exact path entry:

  ```text
  ./secrets.env
  ```

- permit exactly one outbound host through its network policy, with this exact domain entry, and turn the strict allow list on so nothing else gets out:

  ```text
  registry.npmjs.org
  ```

Write the file with any editor or shell technique you like, and make sure it parses as JSON before submitting; a settings file with a syntax error protects nothing.

---

## Solution

### `/root/guarded/.claude/settings.json`

```json
{
  "permissions": {
    "allow": [
      "Bash(ls:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)"
    ]
  },
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "denyRead": [
        "./secrets.env"
      ]
    },
    "network": {
      "allowedDomains": [
        "registry.npmjs.org"
      ],
      "allowManagedDomainsOnly": true
    }
  }
}
```

### How it was created

```bash
mkdir -p /root/guarded/.claude

cat > /root/guarded/.claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": ["Bash(ls:*)"],
    "deny": ["Bash(rm -rf:*)"]
  },
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "denyRead": ["./secrets.env"]
    },
    "network": {
      "allowedDomains": ["registry.npmjs.org"],
      "allowManagedDomainsOnly": true
    }
  }
}
EOF
```

### Verification

```bash
python3 -c "import json; json.load(open('/root/guarded/.claude/settings.json')); print('valid JSON')"
```

Expected output:

```text
valid JSON
```

### Why each part is there

| Setting | Value | Purpose |
| --- | --- | --- |
| `permissions.deny` | `Bash(rm -rf:*)` | Blocks the agent from proposing recursive forced deletes. |
| `permissions.allow` | `Bash(ls:*)` | Lets the agent list directories without prompting. |
| `sandbox.enabled` | `true` | Turns the sandbox on (a JSON boolean, not the string `"true"`). |
| `sandbox.filesystem.denyRead` | `./secrets.env` | Stops any running command from reading the secret file. |
| `sandbox.network.allowedDomains` | `registry.npmjs.org` | The single outbound host a command may reach. |
| `sandbox.network.allowManagedDomainsOnly` | `true` | Strict allow list: nothing outside the list gets out. |

### Notes

- **Permissions vs. sandbox:** permission rules govern what the agent may *propose*, while the sandbox governs what a *running command* can reach. The two layers are independent, so the file configures both.
- **`notes.md`:** no entry is needed, because nothing in the policy restricts it.
- **Strict allow list key:** `allowManagedDomainsOnly` follows the Claude Code settings schema as recalled and was not checked against the current docs. If your Claude Code version names this key differently, adjust it; the rest of the structure does not depend on it.
