# MCP (Model Context Protocol) in Claude Code

**MCP** stands for **Model Context Protocol**. It is a standard way to plug extra tools and data sources into Claude Code. Instead of building every integration into the CLI, Claude talks to small external programs called **MCP servers**, and each server exposes a set of tools Claude can call.

A server can wrap almost anything: your filesystem, a database, a ticketing system, a browser, an internal API. Once a server is connected, its tools show up alongside Claude's built-in ones and Claude can use them during a session.

## Adding a Server

Register a server with `claude mcp add`. The part after `--` is the command Claude runs to start the server:

```bash
claude mcp add files -- npx -y @modelcontextprotocol/server-filesystem /root
```

- `files` is the name you give the server.
- Everything after `--` is how it launches.
- Many servers ship as npm packages, so `npx` pulls them on demand the first time they are needed. You don't install anything by hand.

## Listing and Removing

See what is configured:

```bash
claude mcp list
```

Remove one by name when you no longer need it:

```bash
claude mcp remove files
```

## Where the Config Lives

When you add a server, Claude writes it to `.mcp.json` in the current project (or to your user config, depending on scope). That file is just JSON, so you can:

- Read it.
- Commit the project-scoped one to share with your team.
- Edit it by hand.

The next time you start Claude in that project, the servers are available again.