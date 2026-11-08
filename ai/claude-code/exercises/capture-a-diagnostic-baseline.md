# Task: Capture a Diagnostic Baseline

Record what this machine's Claude Code install looks like while it is healthy, so a future change shows up as a diff.

## Capture

| File | Contents |
| --- | --- |
| `/root/doctor-lab/doctor.txt` | The complete output of the read-only diagnostics command, standard error included so the warnings block is captured too. |
| `/root/doctor-lab/version.txt` | The output of the version flag. |

## Parse

Create `/root/doctor-lab/baseline.md`, a small report holding these six lines, in any order. Each line is written as the lowercase label, a colon, a space, then the value:

```text
version:
commit:
platform:
install path:
search:
auto-update channel:
```

## Rules

- Every value must come out of the two captures, not from memory.
- The version must match what `version.txt` reports.
- The commit and platform values must match the lines of the same name in `doctor.txt` exactly.

# Solution: Capture a Diagnostic Baseline

## Prerequisites

- Claude Code is installed (`which claude` returns a path).
- `/root/doctor-lab/` exists. If not, create it in Step 1.

---

## Step 1: Go to the working directory

```bash
mkdir -p /root/doctor-lab
cd /root/doctor-lab
```

---

## Step 2: Capture the diagnostics output (stdout and stderr)

The task says standard error must be included so the warnings block is captured. Redirect both streams into the file:

```bash
claude doctor > /root/doctor-lab/doctor.txt 2>&1
```

**Why `2>&1`:** Warnings are often written to stderr. A plain `> doctor.txt` would leave them on screen and out of the file. `2>&1` sends stderr to the same place as stdout, and it must come **after** the `>` redirect.

> `claude doctor` is read-only and needs no session, so it is safe to run and works even without a TTY.

If the command shows an interactive screen or waits for a key, run it with a pseudo-terminal wrapper instead, for example:

```bash
script -qc "claude doctor" /dev/null > /root/doctor-lab/doctor.txt 2>&1
```

Use this only if the plain version hangs or produces an empty file.

---

## Step 3: Capture the version

```bash
claude --version > /root/doctor-lab/version.txt 2>&1
```

---

## Step 4: Inspect both captures

```bash
cat /root/doctor-lab/version.txt
echo "-----"
cat /root/doctor-lab/doctor.txt
```

Check that:

- `doctor.txt` is not empty and includes any warnings block.
- `version.txt` has a single version string (for example `2.x.y (Claude Code)`).

Note the exact lines for **commit**, **platform**, **install path**, **search**, and **auto-update channel**. Their wording differs between versions, so read them from your own output.

---

## Step 5: Pull each value out of the captures

Search `doctor.txt` for each field:

```bash
grep -inE "commit|platform|install|path|search|ripgrep|update|channel" /root/doctor-lab/doctor.txt
```

Map them like this:

| Baseline label | Source | What to look for |
| --- | --- | --- |
| `version` | `version.txt` | The version number reported by the version flag |
| `commit` | `doctor.txt` | The line named `commit` (copy the value exactly) |
| `platform` | `doctor.txt` | The line named `platform` (copy the value exactly) |
| `install path` | `doctor.txt` | The line showing where the running install lives |
| `search` | `doctor.txt` | The line showing the search backend in use |
| `auto-update channel` | `doctor.txt` | The line showing the update channel setting |

> Copy values character for character. The task requires `commit` and `platform` to match `doctor.txt` **exactly**, so do not reformat, shorten, or retype them from memory.

---

## Step 6: Write `baseline.md`

Use the lowercase labels, a colon, a space, then the value. Replace the placeholders with the values you found:

```bash
cat > /root/doctor-lab/baseline.md << 'EOF'
version: <value from version.txt>
commit: <value from doctor.txt>
platform: <value from doctor.txt>
install path: <value from doctor.txt>
search: <value from doctor.txt>
auto-update channel: <value from doctor.txt>
EOF
```

Example shape only (your values will differ):

```text
version: 2.1.0
commit: a1b2c3d
platform: linux-x64
install path: /usr/local/bin/claude
search: ripgrep (bundled)
auto-update channel: latest
```

---

## Step 7: Verify the baseline against the captures

Check the file has exactly six lines with the right labels:

```bash
wc -l /root/doctor-lab/baseline.md
cut -d: -f1 /root/doctor-lab/baseline.md | sort
```

Expected labels: `auto-update channel`, `commit`, `install path`, `platform`, `search`, `version`.

Confirm each value really appears in its source:

```bash
# version must match version.txt
cat /root/doctor-lab/version.txt
grep '^version:' /root/doctor-lab/baseline.md

# commit and platform must match doctor.txt exactly
grep -i 'commit'   /root/doctor-lab/doctor.txt
grep '^commit:'    /root/doctor-lab/baseline.md
grep -i 'platform' /root/doctor-lab/doctor.txt
grep '^platform:'  /root/doctor-lab/baseline.md
```

You can also test that a value is present verbatim in the capture:

```bash
grep -F "$(grep '^commit:' /root/doctor-lab/baseline.md | cut -d' ' -f2-)" /root/doctor-lab/doctor.txt
```

---

## Step 8: Use it later as a diff

When something changes (an update, a new PATH entry, a different teammate build), recapture and compare:

```bash
claude doctor > /tmp/doctor-new.txt 2>&1
diff /root/doctor-lab/doctor.txt /tmp/doctor-new.txt
```

---

## Final Checklist

- [ ] `/root/doctor-lab/doctor.txt` exists, is non-empty, and includes stderr (warnings block)
- [ ] `/root/doctor-lab/version.txt` exists and holds the version flag output
- [ ] `/root/doctor-lab/baseline.md` has exactly six lines with lowercase labels, a colon, and a space
- [ ] `version` matches `version.txt`
- [ ] `commit` and `platform` match the same-named lines in `doctor.txt` exactly
- [ ] `install path`, `search`, and `auto-update channel` were copied from `doctor.txt`, not from memory



```bash
#!/usr/bin/env bash
# capture-baseline.sh
# Captures a Claude Code diagnostic baseline into /root/doctor-lab.

set -u

DIR="/root/doctor-lab"
DOCTOR="$DIR/doctor.txt"
VERSION="$DIR/version.txt"
BASELINE="$DIR/baseline.md"

mkdir -p "$DIR"

# --- Capture ---------------------------------------------------------------
# Standard error is included so the warnings block is captured too.
claude doctor > "$DOCTOR" 2>&1 < /dev/null
claude --version > "$VERSION" 2>&1

if [ ! -s "$DOCTOR" ] || [ ! -s "$VERSION" ]; then
  echo "ERROR: a capture file is empty. Check that 'claude' is on PATH." >&2
  exit 1
fi

# --- Parse helpers ---------------------------------------------------------
# get_field <regex of label alternatives>
# Finds the first line in doctor.txt whose label matches, strips everything up
# to the first colon, and trims whitespace. The value is copied verbatim.
get_field() {
  grep -iE "^[^[:alnum:]]*($1)[[:space:]]*:" "$DOCTOR" \
    | head -n 1 \
    | sed -E 's/^[^:]*:[[:space:]]*//; s/[[:space:]]+$//'
}

# The version comes from version.txt, not from doctor.txt.
version="$(grep -oE '[0-9]+\.[0-9]+\.[0-9]+([-+.][0-9A-Za-z.]+)*' "$VERSION" | head -n 1)"

commit="$(get_field 'commit')"
platform="$(get_field 'platform')"
install_path="$(get_field 'install(ation)? path|path|install location|currently running')"
search="$(get_field 'search|search backend|ripgrep')"
channel="$(get_field 'auto-?update channel|update channel|channel')"

# --- Validate --------------------------------------------------------------
missing=0
check() {
  if [ -z "$2" ]; then
    echo "ERROR: could not find a value for '$1' in the captures." >&2
    missing=1
  fi
}
check "version" "$version"
check "commit" "$commit"
check "platform" "$platform"
check "install path" "$install_path"
check "search" "$search"
check "auto-update channel" "$channel"

if [ "$missing" -ne 0 ]; then
  echo "Open $DOCTOR and adjust the label patterns in this script." >&2
  exit 1
fi

# --- Write baseline --------------------------------------------------------
cat > "$BASELINE" << EOF
version: $version
commit: $commit
platform: $platform
install path: $install_path
search: $search
auto-update channel: $channel
EOF

# --- Verify ----------------------------------------------------------------
echo "Baseline written to $BASELINE:"
cat "$BASELINE"
echo

lines="$(wc -l < "$BASELINE")"
[ "$lines" -eq 6 ] || { echo "ERROR: expected 6 lines, got $lines" >&2; exit 1; }

grep -qF "$version"  "$VERSION" || { echo "ERROR: version not found in version.txt" >&2; exit 1; }
grep -qF "$commit"   "$DOCTOR"  || { echo "ERROR: commit not found in doctor.txt" >&2; exit 1; }
grep -qF "$platform" "$DOCTOR"  || { echo "ERROR: platform not found in doctor.txt" >&2; exit 1; }

echo "OK: version, commit, and platform match the captures."


vi /root/doctor-lab/capture-baseline.sh   # paste the script
chmod +x /root/doctor-lab/capture-baseline.sh
/root/doctor-lab/capture-baseline.sh
```