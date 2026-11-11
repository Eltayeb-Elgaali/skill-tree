# Keybindings, Themes, and the Terminal

Every shortcut in the console is an action with an id, and the mapping from keystrokes to actions is a file you own: `/root/.claude/keybindings.json` (`~/.claude/keybindings.json` on any machine). The `/keybindings` command opens the same configuration from inside a session and validates it, refusing bindings that collide.

## The file

```json
{
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+f": "history:search",
        "ctrl+g": null
      }
    }
  ]
}
```

`bindings` is an array of blocks. Each block names one context and maps keystroke strings to action ids inside it. A `null` value unbinds a key rather than pointing it somewhere new. The file may also carry `$schema` and `$docs` fields for editor completion.

## Contexts

A context is the UI state the binding applies to, which is why `Enter` can mean one thing in the prompt and another inside a dialog. The list includes `Global`, `Chat`, `Autocomplete`, `Confirmation`, `Transcript`, `HistorySearch`, `MessageSelector`, `DiffDialog`, `ModelPicker`, `Tabs`, `Attachments`, `Footer`, `Scroll`, `Settings`, `Help`, `Task`, `Select`, `Plugin`, and `ThemePicker`. Bind in the narrowest context that makes sense; `Global` bindings follow you into every dialog.

## Action ids

Action ids are grouped by family, and the family tells you which context they belong to:

| Family | Examples |
|---|---|
| `app` | `app:interrupt`, `app:exit`, `app:toggleTranscript`, `app:toggleTodos` |
| `chat` | `chat:submit`, `chat:newline`, `chat:cycleMode`, `chat:externalEditor`, `chat:stash`, `chat:modelPicker` |
| `history` | `history:search`, `history:previous`, `history:next` |
| `transcript`, `scroll` | `transcript:toggleShowAll`, `scroll:halfPageUp`, `scroll:bottom` |
| `confirm`, `select` | `confirm:yes`, `confirm:no`, `select:next`, `select:accept` |

Keystrokes are written with the modifiers `ctrl`, `alt` (also `meta` or `opt`), `shift`, and `cmd`, joined to the key with `+`. A chord is two keystrokes separated by a space, as in `ctrl+x ctrl+e`.

## The rest of the terminal

Two neighbours of this file are worth knowing. Themes live in `~/.claude/themes/*.json` with a `name`, a `base` chosen from the built-in light, dark, ANSI, and daltonized palettes, and an `overrides` map of colour tokens; `/theme` switches between them. The renderer is separate again: `/tui` moves between the default and fullscreen renderers, and the accessibility options (screen reader mode, reduced motion, flat output, the daltonized themes) are what make a long session usable for the people who need them. Choosing an accessible base theme costs nothing and is worth doing before someone has to ask.

## Files and commands this node introduces

`~/.claude/keybindings.json` with its `bindings`, `context`, and action id fields, `/keybindings`, `~/.claude/themes/*.json`, `/theme`, and `/tui`