# Working Across Multiple Files in Claude Code

Real work spans a repo: a function defined in one file, called from several others, with config off to the side. Point Claude Code at a project and it will search, read, and follow the trail between files on its own.

## How Claude Finds the Right Code

You don't hand it files one by one. It explores the repo the way you would, just faster:

- **Greps** for names, strings, and patterns to locate where something lives.
- **Reads** the matching files, plus the ones they reference.
- **Follows** imports and calls from file to file until it has the full picture.

So if a function is defined in one place and called in another, Claude reads across that boundary without you pasting anything.

## Point It at the Right Place

You steer the search with how you phrase the request:

- Say **"in this repo"** or **"across the codebase"** so it looks widely instead of guessing.
- **Name the file** when you know it: "in `server.py`, the `load_config` function..."
- **Name the symbol** (function, class, or variable) and let Claude find every place it appears.

The more specific your anchor, the less Claude has to search and the tighter its answer.

## Coordinated Multi-File Edits

Once Claude understands how the pieces connect, it can change them together:

- Rename a function and update every caller.
- Add a parameter and thread it through.
- Move logic from one module to another and fix the imports.

It plans the set of edits, shows you the diffs, and applies them across files in one pass. You still review before anything lands, but knowing which files a change touches is what Claude handles best.

## Scoping a Change

To keep an edit small, tell Claude where the edges are:

- "Only touch files under `src/auth/`"
- "Change the parser, not the tests"

Scope is just another anchor. It keeps a coordinated edit from sprawling further than you meant.