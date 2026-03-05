# Morning Brief

**AI-powered morning meeting prep. Zero manual work.**

Every morning, Morning Brief scans your Google Calendar, identifies external meetings, researches each person and their company, and generates a beautiful HTML prep brief — tailored to show how *your specific product* helps each person you're meeting with.

It also creates sample research output artifacts for each meeting: realistic examples of what your product would actually produce for that company, so you can show prospects exactly what they'd get.

---

## What it creates

### Morning Brief HTML
A card for every external meeting — automatically opened in your browser at 7:30 AM.

Each card includes:
- Company context + attendee background
- Discovery questions tailored to their specific role
- Talking points showing how your product helps them
- Links to sample output artifacts

### Sample Study Artifacts
For each meeting, 1–2 standalone HTML files showing realistic output from your product — customized to that company's industry, competitors, and buyer profile.

---

## How it works

1. Reads your company config (`~/.claude/morning-brief-config.md`)
2. Pulls your Google Calendar for today
3. Filters for external meetings (non-company email domains)
4. Researches each attendee and their company in parallel
5. Generates a tailored HTML prep brief
6. Creates 1–2 sample output artifacts per meeting
7. Links everything together and opens it in your browser

---

## Prerequisites

- **[Claude Code](https://claude.ai/code)** — Anthropic's AI coding CLI
- **[Google Calendar MCP](https://github.com/nspect/google-calendar-mcp)** — connects to your Google Calendar via OAuth

> **On billing:** Claude Code is installed per-user and uses your own Anthropic account. Every person who sets this up runs it on their own API key — your usage never affects anyone else's bill.

> **On calendar access:** The Google Calendar MCP authenticates via OAuth with your own Google account. No one else can see your calendar.

---

## Setup (5 steps)

### 1. Install Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Authenticate with your Anthropic account:
```bash
claude
```

### 2. Set up Google Calendar MCP

Follow the [Google Calendar MCP setup guide](https://github.com/nspect/google-calendar-mcp) to connect your own Google account. This gives Claude read access to your calendar.

### 3. Configure your company

```bash
cp config/company.example.md ~/.claude/morning-brief-config.md
```

Open `~/.claude/morning-brief-config.md` and fill in:
- Your company name and email domain
- What your product does (2–3 sentences)
- Your key use cases (4–6)
- Target buyer personas
- Types of sample output artifacts to generate

The more specific you are here, the better the briefs get.

### 4. Install the command

```bash
mkdir -p ~/.claude/commands
cp commands/morning-brief.md ~/.claude/commands/morning-brief.md
```

### 5. Test it manually

Open Claude Code:
```bash
claude
```

Then type:
```
/morning-brief
```

You should see Claude pull your calendar, research your meetings, and generate the HTML brief.

---

## Automate it

### macOS (recommended)

Run the installer — it sets up a launchd agent that fires at 7:30 AM on weekdays:

```bash
bash automation/install-macos.sh
```

To change the time, edit `~/Library/LaunchAgents/com.morningbrief.plist` and update `Hour` and `Minute`.

To view logs:
```bash
tail -f ~/Library/Logs/morning-brief.log
```

To uninstall:
```bash
launchctl unload ~/Library/LaunchAgents/com.morningbrief.plist
```

### Linux / cron

```bash
crontab -e
```

Add:
```
30 7 * * 1-5 /path/to/claude -p "/morning-brief"
```

---

## Configuration reference

Your config file (`~/.claude/morning-brief-config.md`) controls everything. See `config/company.example.md` for the full template with comments.

Key fields:

| Field | What it controls |
|-------|-----------------|
| `Email Domain` | Which attendees are "external" (everyone else) |
| `What We Do` | The pitch angle Claude uses for every meeting |
| `Use Cases` | Claude picks the 2–3 most relevant for each attendee's role |
| `Target Personas` | Helps Claude match use cases to job functions |
| `Sample Study Types` | What kinds of artifacts Claude generates |
| `Competitors` | Used in competitive intel artifacts |

---

## How "sample artifacts" work

For each external meeting, Claude generates 1–2 standalone HTML files that look like real output from your product — but customized to that specific company.

These are conversation tools, not finished deliverables. The idea: instead of *telling* a prospect what your product produces, you *show* them a version built for their company, their industry, their competitors.

Claude picks the most relevant study types based on the attendee's role:
- CMO → messaging test, brand health, ad testing
- Head of Product → concept test, pricing research
- CEO/Founder → buyer persona, competitive intel
- etc.

You configure the study types in your company config.

---

## Customization

**Change what's in each meeting card:** Edit `~/.claude/commands/morning-brief.md` — the prompt that drives everything.

**Change the visual design:** The HTML design system is documented in the command file. You can update colors, layout, and card structure.

**Add more study types:** Add them to the `Sample Study Types` section of your config.

**Add more company context:** The `Notes / Context` section of your config is a freeform field — add pricing, positioning notes, things to avoid, recent news, etc.

---

## Tech stack

| Component | What it does |
|-----------|-------------|
| Claude Code | Runs the AI — orchestrates research, generation, file writing |
| Google Calendar MCP | Reads your calendar events and attendee details |
| Web search + fetch | Researches companies and people before each meeting |
| Bash (`open`) | Opens the brief in your browser when done |
| launchd (macOS) | Schedules the daily run |

No servers. No database. No SaaS subscription. Runs entirely on your machine with your own API keys.

---

## License

MIT — use it, fork it, build on it.

---

*Built by [Jay Flaherty](https://twitter.com/jayflaherty) at [Gather](https://gatherhq.com)*
