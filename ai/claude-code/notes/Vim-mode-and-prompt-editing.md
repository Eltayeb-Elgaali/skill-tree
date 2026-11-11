# Editing the Claude Code Prompt: Summary

The prompt is a text editor, and driving it well keeps long sessions smooth. Pick an editing model with a settings key, not a command, since `/vim` has been removed:

```json
{
  "editorMode": "vim"
}
```

## Vim mode

Vim mode gives the prompt NORMAL and INSERT states. `Esc` leaves INSERT, and the usual keys work on the prompt text.

| Family | Keys |
|---|---|
| Motions | `h j k l`, `w e b`, `0 $ ^`, `gg G`, `f F t T`, `; ,`, `/` |
| Operators | `x dd D`, `dw de db`, `cc C cw ce cb`, `s S`, `yy Y yw ye yb`, `p P`, `>> <<`, `J`, `u`, `.` |
| Text objects | `iw aw iW aW`, `i" a"`, `i' a'`, `i( a(`, `i[ a[`, `i{ a{` |
| VISUAL | `v V`, then `d x y c s p r ~ u U > < J o` |

- Fix a wrong word with `ciw` instead of many backspaces.
- Add a remap for leaving INSERT without `Esc` using the `vimInsertModeRemaps` setting. A common choice is `jk`.

## Readline mode

Leave `editorMode` unset and the prompt acts like a shell line editor, which suits bash users.

| Keys | Effect |
|---|---|
| `Ctrl+A`, `Ctrl+E` | Start and end of line |
| `Ctrl+K`, `Ctrl+U`, `Ctrl+W` | Kill to end, kill line, kill word |
| `Ctrl+Y`, `Alt+Y` | Yank back what you killed |
| `Alt+B`, `Alt+F` | Move a word at a time |
| `Ctrl+_` | Undo the last edit |

## History, multiline, and leaving the prompt

- **History:** `Up` walks back through previous prompts. `Ctrl+R` searches them in reverse. `Ctrl+S` stashes a draft to restore later.
- **Multiline input:** use a trailing `\` before `Enter`, `Option+Enter` or `Shift+Enter` where supported, `Ctrl+J`, or paste text that already has newlines.
- **External editor:** `Ctrl+G` (or `Ctrl+X` then `Ctrl+E`) opens the prompt in `$EDITOR`. What you save comes back as the prompt.
- **Long sessions:** `Ctrl+L` clears the screen but not the conversation. `Ctrl+O` toggles the verbose transcript to show the tool calls behind an answer.

## Lab note

This lab grades configuration, not keystrokes. A check can't watch you press `ciw`, but it can read the settings file that turns vim mode on, so it grades the JSON. Practice the keys in your own console.