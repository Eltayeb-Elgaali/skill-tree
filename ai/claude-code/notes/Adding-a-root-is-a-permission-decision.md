# Scoping added roots

## Summary

`--add-dir` has no read-only mode. The grant covers the whole directory tree for the whole session, so the same access that lets `Grep` walk it lets `Edit` rewrite it and `Bash` run inside it. Treat every added root like an allow rule: grant the narrowest path that covers the job.

```bash
claude --add-dir /work/shared-types
```

This is a grant over one sibling. Adding a parent directory hands over every repo under it, and `--add-dir /` hands over the machine.

## Rules still apply on top

Widening the roots does not switch off the permission system. Deny rules are evaluated against the resulting paths, and **deny always wins**, so a grant plus a targeted deny is a real pattern:

```json
{
  "permissions": {
    "additionalDirectories": ["/work/shared-types"],
    "deny": ["Edit(/work/shared-types/**)"]
  }
}
```

This reads the sibling repo and refuses to modify it, which is usually what "I need the schema" actually means.

Two more boundaries stay where they were:

- A cloned repository's project settings remain gated behind workspace trust, so adding its directory does not activate its allow rules or its hooks.
- The permission mode you started in still governs prompts inside the added tree.

## Added root, worktree, or a second session

| Shape | Use it when | What you give up |
| --- | --- | --- |
| Added root | One task genuinely spans two trees that must stay consistent | A wider blast radius and slower searches |
| Worktree | Two versions of the same repo at once, or work you want isolated | Cross-tree edits in a single conversation |
| Second session | The two jobs are unrelated | Shared context between them |

The deciding question is whether the trees must change together. A checkout service and the shared schema it compiles against must, so one session with an added root fits. Two attempts at the same fix must not share a directory at all, so a worktree fits: `claude -w` starts an isolated one and leaves your main checkout untouched.

## The cost nobody budgets for

Every added root widens what `Glob` and `Grep` will walk. On a large sibling repo that means slower searches and a context window filled with matches from a tree you were only consulting. If the session only needs three files, pointing at those files is cheaper than granting their repo.

## Commands this node introduces

- The `additionalDirectories` array alongside `deny` in the `permissions` block
- The `-w` flag, which starts a session in an isolated worktree
