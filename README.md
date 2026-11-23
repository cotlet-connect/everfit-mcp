# Everfit API integration and MCP server: connect Everfit to ChatGPT, Claude and any AI agent

Everfit API integration and MCP server, unofficial, by Cotlet. Your AI works in your own signed-in Everfit account: clients, programs and workouts, autoflows, check-ins, nutrition, payments and messages, on the same screens your login opens. No developer and no Everfit API key needed. Cotlet is independent and is not affiliated with Everfit.

**Does Everfit have an API or an MCP server?** Everfit's API is for Enterprise customers only, with a token requested from Everfit (checked 23 Nov 2026); it covers clients, programs, autoflows, groups and onboarding, not workout logs, macros, messages or payments. Its Zapier app is a paid plugin on the Studio plan. Everfit has no Make app, no MCP server and no ChatGPT or Claude app; its own AI tools (AI Workout Builder, Program Copilot beta, Ask Olly beta) work inside Everfit. It has no bulk export of clients, programs or the exercise library.

## What online coaches and personal trainers ask their AI to do in Everfit

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Program into your library | "Put this 12-week program from my Google Sheet into my Everfit library. Show me week 1 before you save the rest." | When you write a program |
| Old programs from PDFs | "Turn these program PDFs into Everfit workouts in my library." | Once |
| Who has not logged | "Which clients have not logged a workout this week? Draft a nudge for each. Send nothing yet." | Weekly |
| Macros off target | "List clients whose logged macros were off target this week, with how far off." | Weekly |
| Travel swaps | "Sam is traveling next week. Swap his barbell workouts for the hotel-gym versions." | When a client asks |
| Program after onboarding | "Assign each client who finished onboarding this week to the right program by their goal. Show me first." | Weekly |
| Check-ins in a spreadsheet | "Put this week's check-ins and body weights per client in my Google Sheet." | Your own endpoint, called from Google Sheets |
| New buyers become clients | "Invite the people who bought my plan in Stripe today as Everfit clients with the starter program." | Your own endpoint, called from Stripe or Zapier |
| Sales and failed payments | "Export this month's sales, renewals and failed payments for my bookkeeper." | Monthly |
| Client history out | "Export Sarah's full workout history to a spreadsheet." | When a client asks |
| Leaving Everfit | "Export all my clients, programs and exercise library before I switch platforms." | Once |

Your AI asks you before it sends, books or changes anything. Payments stay in your hands: Cotlet refuses payments.

## Your own Everfit API, built with Cotlet

Everfit's API is Enterprise-only and its Zapier app needs the Studio plan. With Cotlet you build your own: your AI does the job once in your Cloud Browser, you save it as an endpoint, and you call it from Google Sheets, Stripe, GoHighLevel, Mailchimp, Zapier, Make, n8n, your own code.

Examples you can build in a few minutes (none of them exists until you build it):

| Endpoint you build | What it returns or does |
|---|---|
| `workouts-logged` | logged and missed workouts per client for a period |
| `checkins` | check-in answers and body weight per client for a period |
| `macros` | logged macros against targets per client for a period |
| `sales-for-period` | sales, renewals and failed payments for a period |
| `programs-export` | programs with weeks, workouts and exercises |
| `invite-client` | invites a client and assigns the program you name (writes only after you allow it) |

Call it with your Cotlet API key, JSON in and JSON out:

```bash
curl -X POST https://cotlet.dev/api/v1/custom/workouts-logged \
  -H "Authorization: Bearer $COTLET_API_KEY" -H "Content-Type: application/json" -d '{}'
```

Limits: one call runs up to 90 seconds, writes wait for your consent, payments are blocked. Start here: https://cotlet.dev/app/custom-api/new?app=everfit

## Connect in 2 minutes

Sign up at https://cotlet.dev, sign in to Everfit once in your Cloud Browser, then add Cotlet to your AI.

### Claude

Settings → Connectors → Add custom connector, paste `https://cotlet.dev/mcp/v1` and sign in with Cotlet.dev.

### ChatGPT

Open Plugins in ChatGPT, choose Create as a plugin, paste `https://cotlet.dev/mcp/v1` and sign in with Cotlet.dev. No API key needed.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

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

## Questions from Everfit coaches
**Does Everfit have an API or Zapier on my plan?**

Everfit's API is Enterprise-only and its Zapier app is a paid plugin on the Studio plan (checked 23 Nov 2026). With Cotlet you build your own endpoints on any plan and call them from Zapier, Make or n8n.

**Is there an official Everfit MCP server or ChatGPT app?**

No. Everfit's AI tools work inside Everfit. Cotlet is an unofficial MCP server for the ChatGPT and Claude apps and any MCP client.

**Can ChatGPT or Claude put a 12-week program into my Everfit library?**

Yes. Your AI takes your program from a sheet, PDF or notes and builds the workouts in your library, and shows you before it saves.

**Can AI tell me which Everfit clients have not logged a workout?**

Yes. It lists clients who missed or did not log workouts this week and drafts a nudge for each. Nothing goes out until you say yes.

**Can AI read my clients' macro logs in Everfit?**

Yes. It lists clients whose logged macros were off target and by how much.

**How do I export my clients, programs and exercise library from Everfit?**

Everfit has no bulk export. Your AI exports them from the screens your login opens, so you keep everything when you switch.

## Find it on

- [Everfit MCP server on Cotlet.dev](https://cotlet.dev/integrations/everfit-api-mcp-server?utm_source=github&utm_medium=readme&utm_campaign=everfit&utm_content=find)
- [Everfit API with Cotlet](https://cotlet.dev/integrations/api/everfit?utm_source=github&utm_medium=readme&utm_campaign=everfit&utm_content=find)
- [Does Everfit have an API or MCP server?](https://cotlet.dev/api-mcp-status/everfit?utm_source=github&utm_medium=readme&utm_campaign=everfit&utm_content=find)
- [PyPI package everfit-mcp](https://pypi.org/project/everfit-mcp/)

## More fitness coaching apps on Cotlet

- [Trainerize MCP](https://github.com/cotlet-connect/trainerize-mcp)
- [TrueCoach MCP](https://github.com/cotlet-connect/truecoach-mcp)
- [PushPress MCP](https://github.com/cotlet-connect/pushpress-mcp)
- [Exercise.com MCP](https://github.com/cotlet-connect/exercise-com-mcp)
