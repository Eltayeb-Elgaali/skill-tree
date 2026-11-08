## CLAUDE.md 
is your project's memory. Claude reads it at the start of every session, before you type a single word, so anything in it is already known. Write "tests run with make test" once and you stop repeating it in every new conversation.

### Where it lives

Claude looks for memory in a few places and merges what it finds:

| Location | Scope |
| --- | --- |
| `CLAUDE.md` at the repo root | This project, shared with the team via git |
| `.claude/CLAUDE.md` | This project, if you prefer it out of the root |
| `~/.claude/CLAUDE.md` | You, across every project on this machine |
| `CLAUDE.local.md` | Personal instructions for this project; add it to `.gitignore` |

### How to create one

```bash
claude
```
```bash
/init
```

### Adding a memory on the fly

Run /memory to view and edit memory files. For a shared project convention, edit the project's CLAUDE.md and add a clear instruction such as:

```bash
Always use pnpm, never npm, in this repo.
```


Save the file and review the change before committing it. Use `CLAUDE.local.md` instead for personal guidance that should stay out of the shared repository.

### What to put in CLAUDE.md


A good `CLAUDE.md` is **short and high signal**. Every line should be something Claude can't guess from the code, or something it keeps getting wrong. If a line isn't earning its place, delete it.

## What to Include

Think of what a new teammate would ask on day one:

- **Build, run, test commands**: the exact commands (`make build`, `pnpm dev`, `pytest -q`). This is the highest-value content.
- **Project conventions**: e.g. "Use tabs, not spaces", "API routes go under `src/routes/`", "Commits follow Conventional Commits".
- **Gotchas**: e.g. "Dev server hangs without `DATABASE_URL`", "Never edit `generated/` by hand, it is rebuilt".
- **Recurring mistakes**: if you correct Claude twice for the same thing, write it down (e.g. "Don't add new dependencies without asking").

## Keep It Short

- The file is read **every session**, so length is a cost.
- Aim for **one page**, not an essay. Ten real facts beat a hundred lines to wade through.
- Remove anything that stops being true.

## What to Leave Out

- Anything the code already says (e.g. "the `add` function adds two numbers").
- Giant essays, full API docs, or entire style guides. Link to them, or summarize the one rule that actually bites.
- Aspirational rules nobody follows. Describe how the project **really** works today.

## Example Template

```markdown
# Acme API

## Commands
- Build: `make build`
- Test: `pytest -q`
- Run locally: `make dev` (needs `DATABASE_URL`)

## Conventions
- 4-space indent, type hints on all public functions
- New routes go in `src/routes/`, one file per resource

## Gotchas
- `generated/` is rebuilt by `make build`; never edit it by hand
```