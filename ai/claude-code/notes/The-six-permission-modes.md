The `--permission-mode` flag sets a session's overall posture: how much asking happens before the agent edits a file or runs a command. Six named modes exist, and they are not a simple scale from cautious to reckless.

| Mode | File edits | Bash commands | Reach for it when |
|------|------------|---------------|-------------------|
| `default` | ask | ask | Everyday interactive work |
| `acceptEdits` | auto | ask, apart from common file commands | Trusted file work and headless edits |
| `plan` | blocked | blocked | Working out an approach before touching anything |
| `auto` | auto | a classifier decides from policy rules | Long sessions where most prompts are noise |
| `dontAsk` | denied unless a rule allows them | denied unless a rule allows them | Unattended runs that must never sit waiting |
| `bypassPermissions` | auto | auto | Disposable machines, ideally inside a sandbox |

On the command line the first mode is spelled `manual`, which maps to the internal name `default`. The other five are spelled exactly as the table shows them. Inside a session, `Shift+Tab` cycles between the common modes without restarting.

## The three you will use most

`acceptEdits` you met in the last topic: edits land without a prompt, commands still stop for approval. In `plan` mode the agent is read-only. It explores with `Read`, `Grep`, and `Glob`, then presents a plan for you to approve before any mutating tool runs, which is why big changes usually start there. Plan mode gets a full hour later in the course (topic 017).

`auto` is the newest of the three and the least obvious. Rather than asking you about every call, a background classifier decides, using rules you can inspect and override:

```bash
claude auto-mode defaults
```

`claude auto-mode defaults` prints the shipped classifier rules as JSON, `claude auto-mode config` prints the effective set after your own settings, and `claude auto-mode reset` drops your customizations. The related setting `useAutoModeDuringPlan` lets that classifier keep working while a session moves in and out of plan mode.

## The two extremes

`dontAsk` and `bypassPermissions` both remove prompts, in opposite directions. `dontAsk` denies anything that would have prompted, so an unattended run keeps going instead of blocking, and only what your allow rules cover actually executes. `bypassPermissions` skips the checks entirely: every edit and every command runs the moment the model proposes it. The documentation is blunt about the intended use, and so is this course. Bypass belongs on machines you can throw away, and ideally inside the OS sandbox from the third node of this topic. A shared staging server is exactly where it does not belong.

Set a mode per run, interactive or headless:

```bash
claude --permission-mode plan
```

Or make it the machine default with the `defaultMode` key inside the `permissions` block of a settings file. Administrators get the last word through the managed keys `disableBypassPermissionsMode` and `disableAutoMode`, which remove those modes from a fleet no matter what a project file says.

## Modes are the coarse dial

A mode answers one question: how much asking should happen in general. It cannot say "`git status` is always fine but `rm` never is". Statements about individual commands are the job of permission rules, which live in a settings file and are the subject of the next node.

### Settings and commands this node introduces

`--permission-mode` with its six values, `permissions.defaultMode`, `useAutoModeDuringPlan`, `disableBypassPermissionsMode`, `disableAutoMode`, and the `auto-mode` subcommand of `claude`.

## Auto mode is the default now

Recent releases made auto mode the default: routine safe commands run without prompts, while a classifier holds back genuinely destructive ones (a hard `git reset`, `terraform destroy`) for confirmation. Deny and ask rules can now match tool parameters (`Bash(git push:*)` style), so guardrails target the dangerous variant of a command, not the whole tool.