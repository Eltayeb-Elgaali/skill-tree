# skill-tree

My personal learning repo. Notes, command cheat sheets, and small exercises for AI, Cloud, and DevOps.

Bigger hands-on projects live in their own repositories and are linked from the `projects/` folder of each topic and from the `PROJECTS.md` page of each area.

## Structure

| Area | Topics | Projects overview |
|------|--------|-------------------|
| [ai](ai/README.md) | [rag](ai/rag/), [claude-code](ai/claude-code/) | [ai/PROJECTS.md](ai/PROJECTS.md) |
| [cloud](cloud/) | [aws](cloud/aws/), [azure](cloud/azure/), [gcp](cloud/gcp/) | [cloud/PROJECTS.md](cloud/PROJECTS.md) |
| [devops](devops/) | [linux](devops/linux/), [networking](devops/networking/), [shell-scripting](devops/shell-scripting/), [git](devops/git/), [docker](devops/docker/), [kubernetes](devops/kubernetes/) | [devops/PROJECTS.md](devops/PROJECTS.md) |

## Every topic folder has the same layout

- README.md: summary of the topic
- notes/: one file per concept
- commands/: cheat sheets of commands and snippets
- exercises/: small practice files
- projects/: links to hands-on projects (separate repos)

## Conventions

- Folder and file names: lowercase with hyphens.
- Real projects get their own repo, cloned next to this one, never inside it.

## Workflow

1. `git pull`
2. Add or edit notes
3. `git add .` then `git commit -m "describe the change"` then `git push`
