## What a good instruction carries

### A few habits do most of the work:

- Scope the ask. Say what to change and, just as usefully, what to leave alone.
- Give context. Name the files, describe the current behavior, and say what "done" looks like.
- One goal at a time. A prompt that asks for a refactor, a new feature, and a rename at once gives you a tangled result that is hard to review.
- Point at an example. "Match the style of list_users in the same file" is worth a paragraph of description.

## Iterate on the result

The first response is a draft, not a verdict. Read it, run it, and reply with what was off. "Close, but it should read the path from $1 instead of hardcoding it" moves you forward faster than starting over. Working with the agent is a short back-and-forth, not a single perfect prompt.