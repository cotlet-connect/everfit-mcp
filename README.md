# How to connect Everfit to ChatGPT, Claude and any AI agent

**Yes, ChatGPT, Claude and other AI agents can work in your Everfit account.** Cotlet.dev connects your own signed-in Everfit account to any AI agent, so you can check in on quiet clients, review progress and manage coaching activity in plain words.

## What you can ask

> Which clients have been quiet recently, and what are their last workout and check-in dates?

> Show me weight and waist trends for Sam Rivera over the last 30 days.

> Draft Inbox replies for clients who have not checked in this week, and show me the drafts first.

> Assign the next program to the clients in my Monday coaching group after I review the list.

> List active clients with incomplete workouts and summarize their progress for this week.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its Everfit tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"everfit":{"command":"uvx","args":["everfit-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t everfit-mcp .
docker run -i --rm -e COTLET_API_KEY everfit-mcp
```

Tools: `everfit_guide`, `everfit_status`, `everfit_open`, `everfit_read`, `everfit_click`, `everfit_type`, `everfit_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install everfit-mcp        # or run it once: uvx everfit-mcp connect
everfit-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 everfit_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 everfit_agent.py status    # is Everfit ready in your Cotlet.dev account
python3 everfit_agent.py guide     # Everfit screens and sign-in link
python3 everfit_agent.py read      # open Everfit, read the page, close the browser
python3 everfit_agent.py tools     # list Cotlet.dev tools
python3 everfit_agent.py serve     # start the stdio MCP server (same as no argument)
```

## Questions from Everfit users

**Does Everfit have an API for developers?**

Everfit has an API for Enterprise customers on the 500+ client plan. Cotlet.dev works in your own Everfit account directly and exposes coaching clients, workouts, progress, Inbox and task workflows through MCP and REST.

**How do I connect Everfit to ChatGPT?**

Add Cotlet.dev as an app in ChatGPT using the MCP setup on this page, sign in to Everfit once in your Cotlet.dev Cloud Browser, and ask ChatGPT in plain words. No Everfit API key is needed.

**Is there a way to use Claude with Everfit?**

Yes. Add the Cotlet.dev MCP URL as a custom connector in Claude or use the Claude Code command shown above. Claude then works in your own Everfit account.

**Is there an Everfit MCP server?**

Everfit does not publish a public MCP server. Cotlet.dev is a hosted MCP server that works in your Everfit account with ChatGPT, Claude, Cursor, Codex and other AI agents.

## Find it on

- [Everfit on Cotlet.dev](https://cotlet.dev/integrations/everfit-api-mcp-server?utm_source=github&utm_medium=readme&utm_campaign=everfit&utm_content=find)
- [PyPI package everfit-mcp](https://pypi.org/project/everfit-mcp/)
- [Odel](https://odel.app/modules/cotlet/everfit)
- [mcpbeat](https://mcpbeat.com/mcp-servers/cotlet/everfit/)
- [Step-by-step guide (PDF) on Internet Archive](https://archive.org/details/cotlet-connect-everfit-to-ai-agents-guide)

## Also works with


- [All apps connected through Cotlet.dev](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/everfit-api-mcp-server?utm_source=github&utm_medium=readme&utm_campaign=everfit

Start free: https://cotlet.dev/signup?app=everfit&utm_source=github&utm_medium=readme&utm_campaign=everfit
