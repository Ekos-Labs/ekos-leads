# Ekos Leads

Your agent finds the people who want to buy what you sell right now, tells you why each one fits in plain words, and runs LinkedIn outreach to them from your own account. All from chat. Free credits to start, no card.

Works in Claude Code, Claude (claude.ai and desktop), ChatGPT, Grok, Codex, Cursor and OpenClaw.

## What you get

1. **People with a live buying signal.** Hiring, funding, new leaders, growth and more, checked for the people who decide and buy what you sell.
2. **The reason, in plain words.** Every lead comes with the signal behind it and how old it is, so you know why to reach out now.
3. **Checked for fit.** Every lead is checked against your brief before it reaches you. LinkedIn on every lead, email and phone when found.
4. **Outreach from your own LinkedIn.** Your agent drafts the messages with you, then sends them from your account at safe daily limits, stops when someone replies and reports back.

Your agent asks before anything that spends credits or sends a message.

## What you can ask

- "Find me 20 founders in Berlin who run agencies with 11 to 50 people."
- "Who are my best leads this week, and why?"
- "Start a LinkedIn campaign for the top 10 and show me the messages first."
- "How is my outreach doing? Who replied?"

## Install the skill

Claude Code (the plugin brings the skill and the Ekos connection):

```
/plugin marketplace add Ekos-Labs/ekos-leads
/plugin install ekos-leads@ekos
```

Other agents: copy the `skills/ekos-leads` folder into that agent's skills folder. See the [install guide](https://www.ekoslabs.com/skills/install) for each agent.

## Setup (about a minute, in the chat)

Ask your agent "Find me leads".

- **Claude Code, Codex, Cursor, OpenClaw:** your agent asks for your work email, you type the 6-digit code we email you, and it connects Ekos for you. Nothing to copy or paste.
- **Claude (claude.ai or desktop), ChatGPT, Grok:** your agent gives you the two or three steps to add Ekos as a connector, then you sign in and press **Allow**.

Then your agent asks for your website and who you sell to, starts your first leads and shows them to you. Signing up creates your account with free credits. How agents set it up: [ekoslabs.com/agent-signup](https://www.ekoslabs.com/agent-signup).

## What it costs

| | |
| --- | --- |
| To start | 10 free credits, no card. Your first leads are paid from them |
| More leads | Credits you top up when you want them (from $39). 1 credit = $1, credits never expire |
| Monthly fee | None |

## Safety

- You don't need any other API keys or data accounts. Ekos does the searching on its side.
- Your Ekos key is saved privately on your machine (`~/.config/ekos/key`) and in your agent's settings. The skill never shows it in chat or writes it into your project.
- Nothing is spent and nothing is sent on LinkedIn without your clear yes, every time.
- Messages go out under your name, at daily limits that keep your LinkedIn account safe.
- Every step the skill takes is shown in the chat. Nothing runs in the background.

## Need help?

Use **Talk to us** in the app, or [book a call](https://www.ekoslabs.com/?utm_source=github.com&utm_medium=skill&utm_campaign=ekos_leads_readme_support). Ekos can also run the whole thing for you as a done-for-you service.

Files: [`SKILL.md`](SKILL.md) is what your agent reads. [`release.json`](release.json) holds the version.
