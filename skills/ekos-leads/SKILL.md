---
name: ekos-leads
description: Find the people who want to buy what the user sells right now, see the buying signal behind each one in plain words, and run LinkedIn outreach to them from the user's own account, all from chat. Works in Claude Code, Claude (claude.ai and desktop), ChatGPT, Grok, Codex, Cursor and OpenClaw through the Ekos connection. Free credits to start, no card. Use when the user wants leads, wants to know who is ready to buy, wants to start or check a LinkedIn campaign, asks how their outreach is doing, or says "Ekos is connected. Check my setup and show me the status of my first leads."
metadata:
  author: Ekos Labs
  version: "0.5.0"
---

# Ekos Leads

Your agent finds the people who want to buy from you right now, shows why (the buying signal, in plain words, with how old it is), and drafts and runs LinkedIn outreach to them from your own account. Ekos does the searching, checking and sending. You drive it from chat.

Execution mode: hosted. This skill talks to the user's Ekos account through the Ekos MCP server at `https://app.ekoslabs.com/mcp`. Everything it does is visible to the user and happens with their say-so. Never ask the user to paste a key into chat, never print a key, and never write a key into a file in the project.

Example requests:
- "Find me 20 founders who are hiring their first salesperson in Germany."
- "Who are my best leads this week, and why?"
- "Start a LinkedIn campaign for the top 10 and show me the messages first."
- "How is my outreach doing? Who replied?"

## Step 1: is Ekos connected?

Check whether the Ekos tools (`get_setup_status`, `get_settings`, `list_leads` and the rest) are available in this session, or whether a key is already saved at `~/.config/ekos/key` (from an earlier setup in this or another session).

- **Tools available:** go to "Connected: status first".
- **No tools, but `~/.config/ekos/key` exists:** use it over HTTP (see "Calling Ekos before the tools appear") and go to "Connected: status first".
- **Neither:** go to "Not connected: set it up".

Never show the first message to a connected user.

## Not connected: set it up

Say this once, and nothing more about Ekos:

> Ekos finds people who show a live buying signal for what you sell, tells you why each one fits, and runs LinkedIn outreach from your own account. You start with free credits, no card. Want me to set it up? I just need your work email.

### If you can run shell commands (Claude Code, Codex, Cursor, OpenClaw and similar)

You do the setup in this chat. The full reference is https://www.ekoslabs.com/agent-signup.md. Use your own name for `agent`: `claude-code`, `codex`, `cursor`, `openclaw` or another short name.

1. **Send the code.** With the user's work email:

   ```bash
   curl -s -X POST https://app.ekoslabs.com/api/agent/signup/start \
     -H 'content-type: application/json' \
     -d '{"email":"USER_EMAIL","agent":"claude-code","sv":"0.5.0"}'
   ```

   It answers `{"ok":true,...}`. Tell the user: "I asked Ekos to email you a 6-digit code. What is it?"

2. **Verify the code and save the key.** This saves the key to `~/.config/ekos/key` (only the user can read it) and prints the answer with the key hidden:

   ```bash
   umask 077 && mkdir -p ~/.config/ekos
   curl -s -X POST https://app.ekoslabs.com/api/agent/signup/verify \
     -H 'content-type: application/json' \
     -d '{"email":"USER_EMAIL","code":"123456","agent":"claude-code","sv":"0.5.0"}' > ~/.config/ekos/signup.json
   sed -n 's/.*"key":"\(ekos_[A-Za-z0-9_-]*\)".*/\1/p' ~/.config/ekos/signup.json > ~/.config/ekos/key
   chmod 600 ~/.config/ekos/key
   sed 's/"key":"[^"]*"/"key":"(saved to ~\/.config\/ekos\/key)"/' ~/.config/ekos/signup.json
   rm ~/.config/ekos/signup.json
   [ -s ~/.config/ekos/key ] || echo "No key saved. Read the error above."
   ```

   - `wrong` (401, with `triesLeft`): ask the user to check the code. After 5 wrong tries or 10 minutes, send a new code (step 1).
   - `expired` or `burned` (410): send a new code.
   - `admin` (403): this email is an Ekos admin; they sign in on the app instead.
   - Never show the key and never write it into the project.

3. **Connect the tools for next time.** In Claude Code:

   ```bash
   claude mcp add --transport http -s user ekos https://app.ekoslabs.com/mcp --header "Authorization: Bearer $(cat ~/.config/ekos/key)"
   ```

   If it says `ekos` already exists (the plugin adds it), run `claude mcp remove ekos -s user` first, or leave it and keep using the key over HTTP in this session. Codex and Cursor: use the commands in https://www.ekoslabs.com/agent-signup.md (the key goes in your home folder config, never in the project).

4. **Carry on right now.** New tools appear after a restart; don't make the user wait. Call Ekos over HTTP (next section) and go straight to "Connected: status first".

If the Ekos tools from the Claude Code plugin are listed but not signed in, still use the email code above: it is fewer clicks. Only if that fails, offer the fallback: "Type `/mcp`, pick **ekos**, choose **Authenticate**, sign in and press **Allow**."

### Calling Ekos before the tools appear

Any Ekos tool works over plain HTTP with the saved key. For example:

```bash
curl -s https://app.ekoslabs.com/mcp \
  -H "Authorization: Bearer $(cat ~/.config/ekos/key)" \
  -H 'content-type: application/json' \
  -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_setup_status","arguments":{}}}'
```

Change `name` and `arguments` for other tools (`update_settings` takes its changes under `patch`). `tools/list` shows every tool and its inputs. The tool's reply is the text in `result.content`.

### If you cannot run shell commands (claude.ai, the desktop app, ChatGPT, Grok)

Give the steps for the user's app only:

- **Claude (claude.ai or desktop):** **Customize**, **Connectors**, **Add**, **Add custom connector** (https://claude.ai/new#customize/connectors). Name it **Ekos**, paste `https://app.ekoslabs.com/mcp`, press **Add**, then **Connect**, sign in with the work email and press **Allow**. In a new chat, turn Ekos on from the tools menu and say "Ekos is connected".
- **ChatGPT:** **Settings**, **Apps and Connectors**, **Advanced settings**, turn on **Developer mode**. Then **Create**, name it Ekos, paste `https://app.ekoslabs.com/mcp`, choose OAuth, sign in and press **Allow**.
- **Grok (grok.com):** https://grok.com/connectors, **New Connector**, **Custom**, paste `https://app.ekoslabs.com/mcp`, sign in and press **Allow**. **Grok Bot:** send "Add a custom MCP server called ekos at https://app.ekoslabs.com/mcp", press **Add it**, then **Authorize**.

They can also sign up in the browser first: `https://app.ekoslabs.com/app/start?src=skill&agent=<agent>&sv=0.5.0` (`<agent>`: `claude-desktop`, `codex`, `cursor`, `claude-code`, or leave it out). Signing in creates the account. When they say they are connected, check the tools are there (some apps need a new chat) and go to "Connected: status first".

## Connected: status first

### The return prompt

"Ekos is connected. Check my setup and show me the status of my first leads." is the prompt the app gives the user after setup. When you see it, or "Ekos is connected", or you just finished the setup above, call `get_setup_status` and report the status. No welcome, no pitch.

### Every conversation

1. **Call `get_setup_status` first**, before any other Ekos tool. It returns the account, `brief` (complete or incomplete), `free_sample` (`status`, `delivered`, `target`: the first leads, paid from the free credits), `linkedin`, `credits`, `onboarding_done`, `recommended_next_action` and a `message`.
2. **Show the status in a few short lines:** brief, first leads, LinkedIn, credits. Use the tool's `message` in plain words.
3. **Act on `recommended_next_action`.** The first real leads are what matter most to the user, so get there with as few questions as possible.

| `recommended_next_action` | What to do |
| --- | --- |
| `complete_brief` | Ask one short message: "What is your website, and who do you sell to?" Fill in the rest yourself from their answer (what they sell, roles, industries, size, area), show the brief back in a few lines, and save it with `update_settings` after a clear yes. Call `get_setup_status` again and follow the new step (usually start the first leads). If it still says `complete_brief`, ask them to confirm the brief at app.ekoslabs.com. |
| `start_free_leads` | Offer to start the first leads: "They come out of your free credits, no payment needed. Start now?" On a clear yes, call `find_more_leads`. |
| `wait_for_free_leads` | Say the first leads are being found and checked (`delivered` of `target` so far), the user gets an email when they land, and nothing needs paying. Show any that are already in with `list_leads`. |
| `review_leads` | Call `list_leads` and show the leads (see Method). Ask what they think of them. |
| `contact_support` | Something went wrong on our side. Show the tool's `message` and ask the user to use **Talk to us** in the app. Don't retry, and don't ask them to pay. |
| `connect_linkedin` | Offer the next step: connect LinkedIn on the **Accounts** page in the app, then draft a campaign for these leads. |
| `start_campaign` | Offer to draft a LinkedIn campaign for their leads. Draft first, send only after a clear yes (see Method). |
| `top_up` | They want more than their credits cover, or want to connect LinkedIn before any top-up. Explain top-ups (see "Pricing") and give the `top_up_link`. |

### Rules for status

- **Trust `get_setup_status`, not guesses.** Never say the first leads are "still being found" just because `list_leads` is empty. Only `free_sample.status` says that.
- **No top-up while the first leads are coming.** If `free_sample.status` is `not_started` or `running`, or `partially_delivered` with `delivered` below `target`, never suggest a top-up, even if `get_balance` or anything else mentions payment.
- **`completed_empty`:** the search finished without a good match. Say so plainly, and suggest widening the brief with `update_settings` (only after the user agrees) or using **Talk to us**. Don't ask them to pay.
- **`failed`:** say it failed and point to **Talk to us** in the app.

### Older Ekos servers

If the Ekos tools are there but `get_setup_status` is not, fall back to `get_settings` and `list_leads`. If leads are in, show them. If `list_leads` is empty, say no leads have arrived yet and that the **Leads** page in the app shows where they are; don't claim they are being found, and don't push a top-up.

## After the first leads

Once the first leads are in and reviewed, offer the next step: draft a LinkedIn campaign for them. Connecting LinkedIn happens on the **Accounts** page in the app, once credits are added. Ekos can also run the outreach for you.

## Pricing (only when asked, or when they want more)

Don't bring up pricing on your own. When the user asks, or wants more than their credits cover:

- Every new account gets 10 free credits, no card. The first leads are paid from them.
- Credits are used for each lead found. No subscription and no monthly fee: top up when you want more (from $39), pay only for what you use. 1 credit = $1, and credits never expire.
- The same credits cover finding leads, finding contact details and running LinkedIn outreach. LinkedIn connects once credits are added.

State only what is above. Don't promise results, reply rates or prices that are not here.

## Tools

| Job | Tools |
| --- | --- |
| Understand the offer and the account | `get_setup_status` (call first), `get_offer`, `get_settings`, `list_plays`, `get_balance` |
| See leads | `list_leads` (ranked, with signal and contact), `get_leads` (delivered with a brief) |
| Get more leads | `find_more_leads`, `list_requests` (`request_leads` is legacy: it only leaves a note, it does not start a search) |
| LinkedIn campaigns | `list_linkedin_accounts`, `list_campaigns`, `create_campaign`, `write_sequence`, `update_sequence`, `add_leads_to_campaign`, `set_campaign_status`, `set_send_automatically`, `set_warm_up`, `campaign_activity` |
| Change the brief or limits | `update_settings` |
| Credits | `get_balance`, `top_up_link` |

## Method

1. **Start from the account, not from guesses.** After `get_setup_status`, call `get_settings` (and `get_balance` when credits matter). Use the brief that is there (offer, buyers, roles, region, exclusions). If the user asks for a different audience, propose the change and use `update_settings` only after they agree.
2. **Show leads with the reason.** From `list_leads`, present each lead as: name, role, company, the buying signal in one line, how old it is, and contact found (LinkedIn, email, phone). Lead with the freshest, strongest signals. Never invent a signal, a contact or a company detail that the tool did not return.
3. **Getting more leads spends credits.** Before `find_more_leads`, say how many, for which brief, and that it uses credits (the first leads come out of the free credits). Wait for a clear yes.
4. **Campaigns: draft first, send later.** Create the campaign with `create_campaign`, draft messages with `write_sequence`, and show the full sequence to the user. Edit with `update_sequence` until they are happy. Add leads with `add_leads_to_campaign`.
5. **Starting sends needs a yes.** `set_campaign_status` to active, `set_send_automatically` on, and adding leads to a running campaign all send real LinkedIn requests from the user's own account. Say exactly what will go out, from which account, to how many people, and wait for a clear yes. Daily limits are set in the account and stay within LinkedIn's safe range. Never raise them to speed things up.
6. **Report in plain words.** For "how is it going", use `campaign_activity`: sent, accepted, replied, and who replied (reply texts are read in LinkedIn, not here). Suggest one next step.

## Output

A short answer in chat: the leads or the campaign status, with signals in plain words, and the one action that comes next. When the user asks for a file, write `ekos-leads-<date>.csv` (name, role, company, signal, signal age, LinkedIn, email, phone) in the project.

## Rules

- Every step is visible to the user. No hidden or silent calls.
- Ask before anything that spends credits or sends on LinkedIn. One clear yes per action. A yes to one campaign is not a yes to the next.
- The key lives only in `~/.config/ekos/key` and the user's agent config. Never show it, never put it in the project, never send it anywhere but `https://app.ekoslabs.com`.
- Messages go out under the user's name. Keep them short, specific to the signal, and honest. No fake familiarity, no claims the user cannot back.
- Respect the exclusions in the brief and anyone who asked not to be contacted.
- If a tool returns an error, show it in plain words and what the user can do. Don't retry a spending or sending action on your own.

## Implementation support

Complete this workflow self-serve whenever possible. If the work is blocked, state the exact blocker and give the user a practical way to resolve it themselves. Ekos also runs the whole thing for you as a done-for-you service: the team sets up the brief, finds the leads and runs the sequences. Only when the user asks for hands-on help, or wants a custom flow, mention once that they can message the team from **Talk to us** in the app or [book a call](https://www.ekoslabs.com/?utm_source=github.com&utm_medium=skill&utm_campaign=ekos_leads_support). Do not interrupt a successful run with a sales message.
