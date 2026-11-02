# WAJ

WAJ is business management software for salons, spas and barbershops. This
plugin connects Claude or ChatGPT to your WAJ account so you can ask about
your business and make changes in plain language, and it adds skills that
turn WAJ's figures into reviews you can act on.

## What it includes

**The WAJ connector** (`https://mcp.waj.ai/mcp`). You sign in with your WAJ
account and choose read-only access or read and make changes. Your WAJ role
decides which tools you get. It covers bookings and schedules, clients,
services, packages and memberships, staff, leads, marketing workflows,
inventory and reports. ChatGPT and Claude ask before every change, and bulk
or customer-facing changes also wait for your approval in the WAJ app.

**Skills.** In Claude and ChatGPT they load with the plugin; any MCP client connected to WAJ also gets them as prompts from the connector.

| Skill | What it does |
|---|---|
| `sales-analysis` | Revenue for a period against the previous one and how it splits, best and declining services, payment methods, busiest day and money still owed |
| `staff-performance` | Revenue, bookings, booked versus scheduled hours, unique clients and client retention per staff member, with the change against the previous period |
| `business-trends` | Weekly revenue over the last 8 weeks, rising and falling services, new versus returning clients, next week's load and the leads pipeline |
| `daily-briefing` | Today's bookings by staff member, first and last appointment, yesterday's sales, money owed and gift cards expiring this week |
| `client-retention` | Returning clients, how monthly groups of new clients hold up, repeat rates per staff member and clients at risk; builds a win-back audience or workflow only when you ask |
| `stock-check` | Out-of-stock and low-stock items per branch, product sales by category and product cost per service |

## Example prompts

- "Analyze our sales for the last 30 days against the 30 days before."
- "How did each stylist do this month?"
- "What's trending in the business over the last 8 weeks?"
- "Give me today's briefing."
- "Which clients haven't been back in 90 days?"
- "What do I need to reorder?"

## Install

- **Claude (web, desktop and mobile):** add WAJ from the connectors directory at
  https://claude.ai/directory/waj and sign in with your WAJ account.
- **Claude Code:**

  ```
  /plugin install waj --marketplace waj-1/waj-plugin
  ```

  On older versions, run `/plugin marketplace add waj-1/waj-plugin`, then
  `/plugin install waj@waj`.
- **Any other MCP client:** add `https://mcp.waj.ai/mcp` as a remote server
  and sign in when asked.

## Requirements

A WAJ business account with AI connectors switched on by the owner (WAJ app →
Settings → AI connectors). Staff connect with their own login.

## Data and privacy

The plugin contains no code: the skills are instructions, and the only
service it talks to is the WAJ connector at `mcp.waj.ai`, operated by WAJ.
Data stays in your WAJ account and is read or changed only through the tools
your role allows. The skills only read data, except `client-retention`,
which creates an audience or workflow when you ask for one. You can
disconnect at any time from Settings → AI connectors in WAJ.

- Privacy policy: https://waj.ai/privacy-policy
- Terms: https://waj.ai/terms
- Support: https://waj.ai/support or support@waj.ai
