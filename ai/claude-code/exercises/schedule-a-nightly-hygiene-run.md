# Task: Nightly Hygiene Job (cron + headless Claude)

## Context

Notes and files in `/root/webshop`:

```
cat notes.tmp
scratch notes from last sprint

cat cache.tmp
stale cache entries

cat orders.py
# TODO: retry failed order writes
def save_order(order):
    with open("orders.log", "a") as f:
        f.write(str(order) + "\n")

cat app.py
# TODO: validate the cart before checkout
def checkout(cart):
    total = sum(item["price"] for item in cart)
    # FIXME: apply the discount table here
    return total
```

## Build and prove it works

### `/root/recurring/hygiene.sh`

An executable shell script that:

- Sources `/etc/profile.d/litellm.sh` first, so the gateway variables are present even when cron starts it in a non-login shell
- Loops over the files in `/root/webshop` and appends one line per file to `/root/recurring/scan.log`. Each line must contain the word `scanned` and the file's path.
- Runs one headless `claude` call (the `haiku` alias is right for an unattended job) asking for a hygiene report on `/root/webshop`: the TODO and FIXME comments plus the stray `.tmp` files
- Tells the model to begin its reply with the exact line `HYGIENE REPORT`
- Redirects that reply into a dated `.txt` file under `/root/recurring/reports/`, named with today's date so tomorrow's run cannot overwrite it

### Crontab entry

A crontab entry for root that runs `/root/recurring/hygiene.sh` every day at 03:00, by absolute path.

---

# Solution

Run these on the lab VM as root. Not run here, so the expected output below is what you should see.

## 1. Create the script

```bash
mkdir -p /root/recurring/reports

cat > /root/recurring/hygiene.sh <<'EOF'
#!/bin/bash
# Gateway variables first: cron does not start a login shell
. /etc/profile.d/litellm.sh

# Cron's PATH is short; make sure claude can be found
export PATH="$PATH:/usr/local/bin:/root/.local/bin"

SCAN_LOG="/root/recurring/scan.log"
report="/root/recurring/reports/report-$(date +%F).txt"

# One "scanned" line per file
find /root/webshop -type f | while read -r f; do
  echo "scanned $f" >> "$SCAN_LOG"
done

# One headless call; stdout becomes the dated report, stderr goes to a log
claude -p --model haiku --allowedTools "Read,Grep,Glob" \
  "Produce a hygiene report on /root/webshop. List every TODO and FIXME comment (file and text) and every stray .tmp file. Begin your reply with the exact line HYGIENE REPORT, with nothing before it." \
  > "$report" 2>> /root/recurring/cron.log
EOF

chmod +x /root/recurring/hygiene.sh
```

## 2. Install the cron entry (idempotent)

```bash
(crontab -l 2>/dev/null | grep -v '/root/recurring/hygiene.sh'; echo '0 3 * * * /root/recurring/hygiene.sh') | crontab -
```

## 3. Prove it works

### a) The entry is installed

```bash
crontab -l
# expect: 0 3 * * * /root/recurring/hygiene.sh
```

### b) Run it by hand, then check both outputs

```bash
/root/recurring/hygiene.sh

cat /root/recurring/scan.log
# expect one line per file, e.g.:
# scanned /root/webshop/app.py
# scanned /root/webshop/orders.py
# scanned /root/webshop/notes.tmp
# scanned /root/webshop/cache.tmp

ls /root/recurring/reports/
# expect: report-<today's date>.txt

head -1 /root/recurring/reports/report-$(date +%F).txt
# expect exactly: HYGIENE REPORT

cat /root/recurring/reports/report-$(date +%F).txt
# expect: the TODO in orders.py, the TODO and FIXME in app.py, and notes.tmp and cache.tmp
```

### c) Prove it survives cron's bare environment

This is the real test, since the "works by hand, fails on schedule" trap is the main risk:

```bash
rm /root/recurring/reports/report-$(date +%F).txt
env -i HOME=/root PATH=/usr/bin:/bin /root/recurring/hygiene.sh
head -1 /root/recurring/reports/report-$(date +%F).txt   # still HYGIENE REPORT
```

### d) Optional: watch cron fire the job itself

Temporarily schedule it every minute, wait about 60 seconds, then restore the 03:00 entry:

```bash
(crontab -l | grep -v hygiene.sh; echo '* * * * * /root/recurring/hygiene.sh') | crontab -
sleep 70; tail -3 /root/recurring/scan.log
(crontab -l | grep -v hygiene.sh; echo '0 3 * * * /root/recurring/hygiene.sh') | crontab -
```

## Troubleshooting

If the report is empty or missing the header:

- Check `/root/recurring/cron.log` for the claude error.
- Check `command -v claude` to confirm where the binary lives.
- If headless mode refuses to read files because of permissions, widen `--allowedTools` or run the claude call from `/root/webshop`.

## Note

Re-running the script on the same day overwrites that day's report, but tomorrow's run gets a new `report-YYYY-MM-DD.txt` name, so nothing is lost across nights.


### Q : You schedule a nightly cron entry that calls a script wrapping a headless claude run over the day's logs. Run by hand in your terminal, the script works every time. From cron, the job log shows an authentication error. What is the right fix?

- Correct diagnosis and the version that travels. Cron starts jobs with a minimal environment and never reads /etc/profile.d, so the variables your terminal has are missing. Loading the profile inside the script means the job carries its own environment no matter who or what starts it, cron, a hook, or another script.