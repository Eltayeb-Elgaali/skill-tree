# Running Agents on Repeat

Some jobs are the same small task repeated: watching a test suite, polling a log for a new error, or sweeping a project nightly. An agent handles each pass fine. What it needs from you is the **repetition machinery** around it.

## The Pattern

1. Trigger fires
2. Run the task
3. Append result to a file
4. Wait the interval

The pattern has three parts: a **trigger** on an interval, the **task** itself, and an **artifact** that accumulates results. Don't skip the artifact. A repeating run you never look at is just spend, while a log with one line per pass gives you a history you can read, grep, and hand to an agent to summarize.

## Four Ways to Fire the Trigger

| Route | Lives where | Survives a closed terminal | This hour |
|---|---|---|---|
| Shell loop you write | Your terminal or a script | No | Built in the lab |
| `/loop` command | Inside a Claude Code session | No, session must stay open | Covered later |
| Cron entry | The machine's schedule table | Yes | Built in the lab |
| Account-backed routine | Anthropic's infrastructure | Yes, no machine required | Concept only |

The first two need something of yours to keep running. **Cron** fires whether or not you're logged in, making it the right tool for nightly work. **Routines** don't need your machine at all, which is why the lab VM can't demonstrate them.

## Cost

Every iteration that invokes a model is a fresh API call. A loop running `claude -p` every ten seconds burns tokens all day, whether or not anything changed. Two habits keep it cheap:

- **Check cheaply first.** Use plain shell (does the file exist, did the line count change) and call the model only when there's something new to interpret.
- **Cap iterations while developing.** Five passes prove the machinery. Unbounded repetition belongs under a scheduler, not in a forgotten terminal.

## The Lab

The lab builds this split: a shell loop collects, one model call interprets, and cron fires the whole thing on a schedule.


# Shell loops for repetition - Agent Automation

Bash has two loop forms, and both show up constantly in agent automation.

## `while` Loop

Runs as long as its condition holds. With `true` as the condition it runs forever, making it the shape of a **monitor**:

```bash
while true; do
  date >> /root/monitor.log
  sleep 5
done
```

## `for` Loop

Runs a fixed number of passes. It terminates on its own, so it's the right shape while **developing**:

```bash
for i in 1 2 3 4 5; do
  date >> /root/monitor.log
  sleep 1
done
```

It can also walk a list of files. A glob expands to every matching path, and the body runs once per file:

```bash
for f in /root/project/*; do
  echo "scanned $f" >> /root/monitor.log
done
```

## Key Pieces

- `date` prints a timestamp, making each pass identifiable.
- `>>` appends to a file instead of overwriting it. A single `>` would leave only the final pass, so recurring runs almost always want `>>`.
- `sleep` pauses for the given number of seconds, setting the interval.

## Watching the Results

Count recorded passes with `wc -l` (reading from a redirect):

```bash
wc -l < /root/monitor.log
```

`watch` reruns a command every two seconds and repaints the screen, which is handy for eyeballing a log while your loop feeds it. Press Ctrl+C to exit.

## Loops in Scripts

Anything you'd type twice belongs in a script. Put the shebang first, the loop under it, mark it executable with `chmod +x`, and run it by path:

```bash
#!/bin/bash
for i in 1 2 3 4 5; do
  date >> /root/monitor.log
  sleep 1
done
```

A scripted loop is also the handoff point to the agent: the loop gathers data cheaply in shell, then a single headless `claude -p` run reads the accumulated file and interprets it. Shell for collection, one model call for interpretation: the cost habit from the previous node in working form.

## Commands Introduced

`while` and `for` loops, `sleep`, `date`, the `>>` append redirect, `wc -l`, and `watch`.


# Cron schedules and the environment trap

**Cron** is a Linux daemon that wakes every minute and runs whatever tasks are due in the schedule table, with no terminal or login required.

## Five Fields

Each cron line is five time fields followed by the command:

```
minute  hour  day-of-month  month  day-of-week   command
```

`*` means "every". Examples:

- `* * * * *` runs every minute
- `0 3 * * *` runs at 03:00 daily
- `0 9 * * 1` runs at 09:00 every Monday
- `*/5 * * * *` runs every five minutes

## Managing the Table

- `crontab -l` lists the current table (read-only, safe).
- `crontab -r` removes the table entirely.
- `crontab -` replaces the table from stdin. To append safely without an editor:

```bash
(crontab -l 2>/dev/null; echo '0 3 * * * /root/maintenance/hygiene.sh') | crontab -
```

`2>/dev/null` hides the error `crontab -l` gives when no table exists yet, so the first entry still installs.

## The Environment Trap

Jobs that work by hand but silently fail on schedule are usually an environment problem. Cron provides a minimal environment: short `PATH`, no login profile, no terminal, and nothing from `/etc/profile.d`. Here, the gateway variables that let `claude` authenticate live in `/etc/profile.d` and are read only by login shells.

**Fix 1:** Source the profile as the script's first line (the lab's approach):

```bash
. /etc/profile.d/litellm.sh
```

**Fix 2:** Load it from the cron entry with a login shell (`-l`) and a command string (`-c`):

```
* * * * * bash -lc '/root/maintenance/hygiene.sh' >> /root/maintenance/cron.log 2>&1
```

**Good habits:**

- Use absolute paths, since cron doesn't start in your home directory.
- Redirect output to a log, since there is no terminal. `2>&1` merges stderr into the same file, and the log is the only evidence a failing job leaves.

## Output That Piles Up Sensibly

Use one file per run with a collision-proof name. `date +%F` prints e.g. `2026-08-17`, which also sorts correctly:

```bash
report="/root/maintenance/reports/report-$(date +%F).txt"
```

## Commands Introduced

- Five-field schedule format
- `crontab` with `-l`, `-` (stdin), and `-r`
- `.` to source a profile script
- `bash -lc`
- `2>&1` redirect
- `date +%F`


# The loop command and account-backed routines


Claude Code has its own repetition tools, so check what it already does before writing shell for the same job.

## /loop

`/loop` reruns a prompt or slash command on an interval, from inside a live session. The interval is a duration or a five-field cron expression:

```
/loop 5m /babysit-prs
/loop 0 * * * * Summarize new errors in logs/app.log
```

- **No interval:** Claude paces itself and decides when the next pass is due.
- **No prompt either:** it falls back to a default maintenance prompt from `.claude/loop.md` in the project, or `~/.claude/loop.md` for you personally. The project file wins if both exist.
- **Jitter:** scheduled wakes carry a deterministic jitter so many sessions don't fire on the same second.
- **Expiry:** a schedule expires after seven days.
- **Kill switch:** `CLAUDE_CODE_DISABLE_CRON=1` removes the cron tools and `/loop`, and stops anything already pending.
- **Key limitation:** `/loop` belongs to a live session. Close the terminal and the repetition ends, which is the gap cron fills.
- **Availability:** built-in commands vary by build and plan, so confirm with `/help` in your own install.

## Account-Backed Routines

Routines are the hosted version. `/schedule` and `/routines` create and manage recurring agents that run on Anthropic's infrastructure instead of your machine.

- **Triggers:** hourly, daily, weekday, weekly, custom cron, or one-off. A routine can also start from a GitHub event or an authenticated API call to its own trigger endpoint.
- **Response:** it returns the id and URL of the web session it created.
- **Lab limitation:** routines are tied to a claude.ai account. This VM authenticates through a lab gateway, so `/schedule` can't create routines here.
- **Why it matters:** it is the only one of the four routes that keeps running when your machine is off.

## Choosing

| Job | Route |
|---|---|
| Watch something for the next ten minutes while you work | Shell loop, or `/loop` if you want the model each pass |
| Repeat a prompt during a session, with the model judging cadence | `/loop` with no interval |
| Nightly hygiene on a machine you control | cron plus a headless run |
| Recurring work that must survive your laptop being closed | Account-backed routine |

The lab builds the third row, since it is the one this VM can prove end to end, and a cron entry plus a headless call is the shape most teams actually ship.