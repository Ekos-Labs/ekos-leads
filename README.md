# Ekos Leads

Your AI finds the people who want to buy from you right now, shows the buying signal behind each one, and runs LinkedIn outreach from your own account. First 5 leads free, no card.

Works in Claude Code, Claude (claude.ai and desktop), ChatGPT, Grok, Codex, Cursor and OpenClaw.

## Install

**Easiest: give this link to your AI.** Paste it into Claude Code, Codex or Cursor and say "install this skill":

```
https://github.com/Ekos-Labs/ekos-leads
```

**Claude Code (plugin, skill and connection in one):**

```
/plugin marketplace add Ekos-Labs/ekos-leads
/plugin install ekos-leads@ekos
```

Then type `/mcp`, pick **ekos**, choose **Authenticate**, sign in to Ekos and press **Allow**.

**Clone it:**

```bash
git clone https://github.com/Ekos-Labs/ekos-leads.git
cp -r ekos-leads/skills/ekos-leads ~/.claude/skills/
```

For Codex, Cursor or another agent, copy `skills/ekos-leads` into that agent's skills folder.

**Claude app (claude.ai or desktop):** download [ekos-leads.zip](https://www.ekoslabs.com/skills/ekos-leads.zip), then **Settings, Capabilities, Skills, Upload skill**.

## Then

Ask your AI "Find me leads". It connects you to Ekos in about a minute (sign in, press Allow, nothing to paste), sets up your brief in the chat and starts your free leads.

Step-by-step pictures for every app: [app.ekoslabs.com/app/agent](https://app.ekoslabs.com/app/agent). What the skill does and costs: [skills/ekos-leads/README.md](skills/ekos-leads/README.md).

## License

MIT. Part of [Ekos-Labs/skills](https://github.com/Ekos-Labs/skills).
