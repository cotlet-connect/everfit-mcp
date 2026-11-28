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

## What you can ask your AI to do in Everfit

These are real jobs Everfit users asked about. Cotlet lets ChatGPT, Claude or any MCP client do them in your own signed-in Everfit account.

### Programs and workouts

#### Can ChatGPT or Claude write a 12-week program and put it into my Everfit library for me?

Yes. Everfit's AI Workout Builder makes one workout at a time. With Cotlet, ChatGPT or Claude writes the 12 weeks and your agent builds each workout in your signed-in Everfit Workout Builder and library. Nothing is saved until you say yes.

Ask your agent: "Write a 12-week strength program with 3 workouts a week and build each workout in my Everfit library. Show me week 1 first."

#### Can AI turn my old programs from Google Sheets, PDFs or Notes into Everfit workouts without retyping each one?

Partly. Everfit's PDF import turns one PDF into one workout, and it has no CSV import for programs. With Cotlet, your agent reads your sheet, PDF or notes and builds each workout in your signed-in Workout Builder. Nothing is saved until you say yes.

Ask your agent: "Take the programs in this sheet and build each workout in my Everfit Workout Builder with its exercises, sets and reps. Show me the first one."

#### Can an AI agent swap exercises in a client's Everfit program when they message me that they are travelling?

Yes. With Cotlet, your agent opens the client's training calendar in your signed-in Everfit account and replaces the exercises that need a gym with ones that suit hotel or bodyweight training. Nothing changes until you say yes.

Ask your agent: "Open this client's Everfit program for next week and swap gym exercises for hotel-room alternatives. Show me each swap first."

#### Can an AI assistant assign my clients to the right program or Autoflow when they finish onboarding in Everfit?

Yes. Everfit's Onboarding Flow and Autoflows can already start a program. With Cotlet, your agent checks each client's intake answers in your signed-in account and assigns the program or Autoflow that fits. Nothing is assigned until you say yes.

Ask your agent: "List Everfit clients who finished onboarding this week, pick the right program or Autoflow for each from their answers and assign it."

As an API endpoint: Yes. Save the onboarding check as an endpoint in Cotlet and call it every morning from n8n, Make or Zapier.

#### How do I let clients move or swap workouts in Everfit without messaging me each time?

Yes. Everfit's client settings decide whether a client may replace an exercise, move days or create workouts. With Cotlet, your agent opens each client in your signed-in account and turns the right settings on. Nothing changes until you say yes.

Ask your agent: "Turn on exercise swaps and moving workouts for all my active Everfit clients. Show me the list of clients first."

### Check-ins and progress

#### Can I connect ChatGPT or Claude to Everfit to ask which clients have not logged a workout this week?

Yes. With Cotlet, ChatGPT or Claude reads your signed-in Everfit account, lists the clients with no workout logged this week and drafts a short check-in for each. Nothing is sent until you say yes.

Ask your agent: "List my Everfit clients who logged no workout this week, with their program and last workout, and draft a short check-in for each."

As an API endpoint: Yes. Save the list as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier to get the quiet clients by email.

#### Can AI read my clients' macro logs in Everfit and tell me who is off target this week?

Yes. With Cotlet, your agent reads the Macros journal in your signed-in Everfit account and lists the clients whose logged calories or macros were off target this week, and by how much. It only reads.

Ask your agent: "From the Everfit Macros journal, list clients whose calories or protein were more than 15% off target this week, and by how much."

As an API endpoint: Yes. Save the macro check as an endpoint in Cotlet and call it every Sunday from n8n, Make, Zapier or a script.

#### How do I automatically send check-in reminders and forms to every client every week in Everfit?

Partly. Everfit's Autoflows and form triggers already send forms on a schedule. With Cotlet, your agent sets them up for each client in your signed-in account and lists clients who have not answered. Nothing changes until you say yes.

Ask your agent: "Set up a weekly check-in form for all my active Everfit clients and list who has not answered last week's form."

As an API endpoint: Yes. Save the unanswered check-in list as an endpoint in Cotlet and call it every Tuesday from n8n, Make or Zapier.

### Onboarding and sales

#### How do I connect Jotform or Typeform to Everfit so a signed waiver sends the client their invite link?

Yes. With Cotlet, your agent reads the new signed waivers in Jotform or Typeform and invites each person as a client in your Everfit account, both signed in in your Cloud Browser. Nothing is invited until you say yes.

Ask your agent: "Read today's signed waivers in Jotform and invite each person as a client in Everfit. Show me the list before inviting anyone."

As an API endpoint: Yes. Save the waiver check as an endpoint in Cotlet and call it every hour from n8n, Make or Zapier.

#### How do I connect Stripe or my payment tool to Everfit so new buyers are added as clients and invited without me?

Yes. Everfit's Zapier add-on is on the Studio plan only. With Cotlet, your agent reads new buyers in Stripe and adds and invites each one as a client in your Everfit account, both signed in in your Cloud Browser. Nothing is invited until you say yes.

Ask your agent: "List new Stripe buyers from the last 7 days and add each one as a client in Everfit. Stop before sending invites."

As an API endpoint: Yes. Save the new-buyer step as an endpoint in Cotlet and call it from n8n or Make whenever Stripe records a payment.

#### How do I connect Everfit to Mailchimp or GoHighLevel so new clients get my welcome sequence?

Yes. With Cotlet, your agent reads new clients in your signed-in Everfit account and adds them to Mailchimp or GoHighLevel, both signed in in your Cloud Browser, so your welcome sequence starts. Nothing is added until you say yes.

Ask your agent: "Add every client who joined Everfit this week to my Mailchimp audience with name and email, tagged as new client."

As an API endpoint: Yes. Save the new-client read as an endpoint in Cotlet and call it daily from n8n or Make to push clients into your email tool.

#### How do I bulk import a list of clients into Everfit from a CSV?

Yes. Everfit has Add Multiple Clients with a CSV template. With Cotlet, your agent fills that template from your list, adds the clients in your signed-in account and tells you which rows it could not use. Nothing is added until you say yes.

Ask your agent: "Take the clients in this CSV and add them to Everfit with Add Multiple Clients. Show me any rows that do not fit first."

#### How do I get a report of every sale, renewal and failed payment from Everfit into my accounting?

Partly. Everfit's Payments Sales Analytics has an Export button that emails a file kept for 7 days. With Cotlet, your agent reads the sales it shows in your signed-in account and enters them in your sheet or accounting tool. Nothing is entered until you say yes.

Ask your agent: "Read last month's sales in Everfit Sales Analytics and add one row per sale to my Google Sheet with date, client, package and amount."

As an API endpoint: Yes. Save the sales read as an endpoint in Cotlet and call it monthly from n8n, Make or Zapier to send the rows to your accounting.

### Exports and your data

#### How do I export all my clients, programs and exercise library from Everfit before I switch to another platform?

Yes. Everfit has no bulk export for clients, programs or the exercise library. With Cotlet, your agent goes through your signed-in account page by page and saves each list and each program as a file you keep.

Ask your agent: "Save my Everfit client list, every program with its workouts and my exercise library as separate files before I switch platforms."

#### How do I export a client's full workout history from Everfit into a spreadsheet?

Yes. Everfit prints one workout at a time as a PDF. With Cotlet, your agent reads the client's training calendar in your signed-in account and puts every logged workout, with exercises, weights and reps, into one spreadsheet.

Ask your agent: "Export this client's full Everfit workout history into one spreadsheet with date, exercise, sets, reps and weight."

As an API endpoint: Yes. Save the history read as an endpoint in Cotlet that takes a client name, and call it from n8n, Make or a script.

#### How do I connect Everfit to Google Sheets so my client check-ins and weights land in one spreadsheet automatically?

Yes. With Cotlet, your agent reads the new check-in forms and weights in your signed-in Everfit account and adds them as rows to your Google Sheet, signed in in the same Cloud Browser. Nothing is added until you say yes.

Ask your agent: "Add this week's Everfit check-in answers and weights to my Google Sheet, one row per client."

As an API endpoint: Yes. Save the weekly read as an endpoint in Cotlet and call it every Sunday from n8n, Make or Zapier to fill the sheet.

#### How do I move my clients' macro and nutrition logs out of Everfit, or sync them with MyFitnessPal automatically?

Partly. Everfit's MyFitnessPal and Cronometer links sit in the client's own app, and Cotlet cannot change them. With Cotlet, your agent reads each client's Macros journal in your signed-in account and saves the logs as a spreadsheet you keep.

Ask your agent: "Save every client's Everfit macro and meal logs for the last 30 days as one spreadsheet, one row per day and client."

As an API endpoint: Yes. Save the nutrition read as an endpoint in Cotlet and call it weekly from n8n, Make or Zapier to copy the logs into a sheet.

### Wearables and calendars

#### How do I connect Everfit to Garmin, Strava or Apple Health so my clients' workouts show up without logging by hand?

Partly. Everfit syncs selected metrics from Apple Health, Garmin and Fitbit in the client's own app, and Cotlet cannot change that sync. With Cotlet, your agent reads what reached each client's log in your signed-in account and lists clients with gaps. It only reads.

Ask your agent: "List the Everfit clients with no logged workout or steps in the last 5 days, so I can check their wearable sync."

As an API endpoint: Yes. Save the gap check as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier.

#### How do I connect Everfit to Acuity, Calendly or Google Calendar so check-in calls and sessions are booked automatically?

Partly. Everfit has its own Booking and Scheduling features and a Google Calendar integration. With Cotlet, your agent reads your upcoming sessions in Everfit and adds or updates the events in Google Calendar or Acuity, signed in in the same Cloud Browser. Nothing is added until you say yes.

Ask your agent: "Copy next week's Everfit sessions and check-in calls into my Google Calendar, one event per session with the client name."

As an API endpoint: Yes. Save the sessions read as an endpoint in Cotlet and call it daily from n8n or Make to update your calendar.

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
