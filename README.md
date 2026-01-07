# 🔨 BuildFlow

**Turn interesting links into working code.** A browser extension + Telegram bot that sends URLs and X/Twitter posts to OpenCode for automated research, implementation, and report generation.

Found an interesting tutorial? Click a button. See a cool project on X? One click. BuildFlow sends it to OpenCode, which researches, implements key examples, runs tests, and generates a comprehensive report.

---

## Name Ideas

| Name | Vibe |
|------|------|
| **BuildFlow** | Smooth flow from discovery to implementation |
| **LearnForge** | Forging knowledge into code |
| **CodeHarvest** | Harvesting code from the web |
| **ClickToCode** | One click to working code |
| **BuildBridge** | Bridge between discovery and implementation |
| **Distillery** | Distilling articles into working code |
| **SparkBuild** | Spark of inspiration → built project |
| **InkToCode** | Written content to executable code |

---

## What It Does

```
┌──────────────────────────────────────────────────────────────────┐
│                         YOU DISCOVER                              │
│                                                                  │
│   📄 Interesting article    🐦 Cool X post    📦 GitHub repo     │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│                        ONE CLICK                                  │
│                                                                  │
│   🔘 Browser extension popup    🔨 Button on X tweets            │
│   📱 Send URL to Telegram bot                                    │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│                      OPENCODE WORKS                               │
│                                                                  │
│   📖 Reads the content                                           │
│   💡 Identifies key concepts                                     │
│   🛠️  Implements examples                                        │
│   🧪 Runs tests                                                  │
│   📝 Generates report                                            │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│                        YOU GET                                    │
│                                                                  │
│   research/                                                      │
│   └── project-name-abc123/                                       │
│       ├── REPORT.md        # What it is, how it works            │
│       ├── src/             # Working implementation              │
│       └── repo/            # Cloned repositories                 │
└──────────────────────────────────────────────────────────────────┘
```

---

## Architecture

```
┌─────────────────────┐     ┌─────────────────────┐
│  Chrome Extension   │     │    Telegram Bot     │
│                     │     │                     │
│  • Popup button     │     │  • Send any URL     │
│  • X.com injection  │     │  • /status command  │
│    (🔨 on tweets)   │     │  • /history command │
└──────────┬──────────┘     └──────────┬──────────┘
           │                           │
           │    POST /api/learn        │
           └─────────────┬─────────────┘
                         ▼
           ┌─────────────────────────┐
           │    Backend Server       │
           │    (localhost:3456)     │
           │                         │
           │  • Express API          │
           │  • SQLite history       │
           │  • Session polling      │
           │  • Notifications        │
           └─────────────┬───────────┘
                         │
                         ▼
           ┌─────────────────────────┐
           │    OpenCode Server      │
           │    (localhost:4096)     │
           │                         │
           │  • Creates session      │
           │  • Runs research agent  │
           │  • Generates output     │
           └─────────────┬───────────┘
                         │
                         ▼
           ┌─────────────────────────┐
           │   ./research/[slug]/    │
           │                         │
           │   REPORT.md             │
           │   src/                  │
           │   repo/                 │
           └─────────────────────────┘
```

---

## Packages

| Package | Description |
|---------|-------------|
| `@build-learn/shared` | Shared types, prompt templates, utilities |
| `@build-learn/chrome-extension` | Chrome Extension (Manifest V3) |
| `@build-learn/telegram-bot` | Telegram bot (Telegraf) |
| `@build-learn/backend` | Express server with SQLite |

---

## Prerequisites

- **Node.js** 20+
- **pnpm** 9+
- **[OpenCode](https://opencode.ai/)** - AI-powered coding assistant (required)
- **[Sisyphus Agent](https://github.com/code-yeongyu/oh-my-opencode)** - Enhanced agentic experience (recommended)
- **Telegram Bot Token** (optional, for Telegram integration)

### Installing OpenCode

OpenCode is the core AI engine that powers BuildFlow's research capabilities.

```bash
# Install OpenCode
curl -fsSL https://opencode.ai/install | bash

# Or via npm
npm install -g opencode
```

After installation, configure your AI provider (Anthropic, OpenAI, etc.) by running `opencode` and following the setup prompts.

For more details, visit [opencode.ai](https://opencode.ai/).

### Installing Sisyphus Agent (Recommended)

For the best agentic experience, install the Sisyphus agent from [oh-my-opencode](https://github.com/code-yeongyu/oh-my-opencode). Sisyphus provides enhanced orchestration capabilities, parallel task execution, and smarter delegation patterns.

```bash
# Clone oh-my-opencode
git clone https://github.com/code-yeongyu/oh-my-opencode.git ~/.opencode/oh-my-opencode

# The agent files will be available in your OpenCode configuration
```

Visit the [oh-my-opencode repository](https://github.com/code-yeongyu/oh-my-opencode) for detailed setup instructions and available agents.

---

## Installation

### 1. Clone and install

```bash
git clone https://github.com/BowTiedSwan/buildflow.git
cd buildflow
pnpm install
```

### 2. Build all packages

```bash
pnpm build
```

### 3. Configure environment

```bash
cp .env.example .env
```

Edit `.env`:
```bash
# Required for Telegram bot (optional feature)
TELEGRAM_BOT_TOKEN=your_token_from_botfather

# OpenCode server URL (default works if running locally)
OPENCODE_URL=http://localhost:4096

# Backend port
PORT=3456
```

---

## Running

### Step 1: Start OpenCode

OpenCode must be running for research to work. Start it in the directory where you want research outputs:

```bash
cd ~/my-research-workspace
opencode serve --port 4096
```

Or run the full TUI (includes server):
```bash
cd ~/my-research-workspace
opencode
```

> **Important:** Research outputs (`./research/[slug]/`) are created relative to OpenCode's working directory.

> **⚠️ Multiple OpenCode Instances:** If you have multiple OpenCode TUI windows open, only ONE can serve port 4096. Sessions created via the API will be bound to whichever instance owns the port. For best results, use a single dedicated OpenCode instance for BuildFlow. See [Troubleshooting](#sessions-not-appearing-in-opencode-tui) if sessions don't appear.

### Step 2: Start the Backend

```bash
pnpm backend
```

You should see:
```
🚀 BuildFlow Backend
========================
📡 Server:     http://localhost:3456
🔗 OpenCode:   http://localhost:4096

Endpoints:
  GET  /api/health       - Health check
  POST /api/learn        - Start new research
  GET  /api/learn        - List research sessions
  GET  /api/learn/:id    - Get session details
```

### Step 3: Load Chrome Extension

1. Open `chrome://extensions`
2. Enable **Developer mode** (top right)
3. Click **Load unpacked**
4. Select `packages/chrome-extension/dist/`

### Step 4: Start Telegram Bot (optional)

```bash
pnpm bot
```

---

## Usage

### Chrome Extension - Any Website

1. Navigate to any webpage (article, tutorial, GitHub repo)
2. Click the BuildFlow extension icon
3. Click **"Research & Build"**
4. Check OpenCode for progress

### Chrome Extension - X/Twitter

1. Browse X.com (twitter.com)
2. Find an interesting tweet
3. Click the 🔨 button next to the tweet's timestamp
4. The tweet text and any URLs are sent for research

### Telegram Bot

1. Start a chat with your bot
2. Send any URL:
   ```
   https://example.com/interesting-article
   ```
3. Or forward/paste an X post URL
4. Bot confirms and notifies when complete

**Commands:**
- `/start` - Welcome message
- `/status` - Check if backend is running
- `/history` - View recent research sessions

---

## Research Output

Each research task creates a folder:

```
research/
└── article-title-abc123/
    ├── REPORT.md          # Comprehensive report
    │   ├── Summary
    │   ├── Key Concepts
    │   ├── Implementation Details
    │   ├── Test Results
    │   ├── Applications
    │   └── Assessment
    │
    ├── src/               # Implemented examples
    │   └── example.ts
    │
    └── repo/              # Cloned repositories (if applicable)
        └── cloned-repo/
```

### Sample REPORT.md

```markdown
# Research Report: React Server Components Guide

## Summary
A comprehensive guide to React Server Components, explaining the 
mental model, use cases, and implementation patterns.

## Key Concepts
- Server Components run only on the server
- Client Components are marked with "use client"
- Data fetching happens at the component level

## Implementation Details
### What I Built
A demo app showing server/client component interaction.

### Key Files
- `src/app/page.tsx` - Server component with data fetching
- `src/components/Counter.tsx` - Client component with state

### How to Run
cd src && npm install && npm run dev

## Test Results
- [x] Server component renders correctly
- [x] Client component hydrates properly
- [ ] Streaming not tested (requires specific setup)

## Assessment
**Quality:** ⭐⭐⭐⭐⭐
**Usefulness:** ⭐⭐⭐⭐
**Recommendation:** Essential reading for React developers
```

---

## API Reference

### `POST /api/learn`

Start a new research session.

**Request:**
```json
{
  "type": "url",
  "source": "chrome_extension",
  "url": "https://example.com/article"
}
```

Or for X posts:
```json
{
  "type": "x_post",
  "source": "telegram",
  "text": "Tweet text here...",
  "containedUrls": ["https://github.com/example/repo"],
  "telegramChatId": 123456789
}
```

**Response:**
```json
{
  "success": true,
  "requestId": "uuid",
  "sessionId": "ses_xxxxx",
  "title": "Build & Learn: Example Article"
}
```

### `GET /api/learn`

List research sessions.

**Query params:** `?limit=50`

**Response:**
```json
{
  "sessions": [
    {
      "id": "uuid",
      "title": "Build & Learn: ...",
      "status": "completed",
      "createdAt": "2024-01-07T..."
    }
  ],
  "total": 1
}
```

### `GET /api/learn/:id`

Get specific session details.

### `GET /api/health`

Health check.

**Response:**
```json
{
  "status": "ok",
  "opencode": "connected",
  "activePolls": 0
}
```

---

## Custom OpenCode Agent

The project includes a custom OpenCode agent at `.opencode/agent/research-builder.md`:

```yaml
description: Researches URLs, articles, tutorials, and X posts...
mode: subagent
tools:
  - read, write, edit, bash, glob, grep, webfetch
permissions:
  bash: allow (except destructive commands)
  webfetch: allow
```

This agent is optimized for:
- Fetching and reading web content
- Cloning repositories
- Running tests
- Generating structured reports

---

## Development

### Run all in development mode

```bash
pnpm dev
```

### Run individual packages

```bash
pnpm backend          # Backend server
pnpm bot              # Telegram bot
pnpm extension:dev    # Chrome extension (watch mode)
```

### Type checking

```bash
pnpm typecheck
```

### Build for production

```bash
pnpm build
```

---

## Troubleshooting

### "Cannot connect to backend"

- Ensure backend is running: `pnpm backend`
- Check port 3456 is not in use

### "Failed to create OpenCode session"

- Ensure OpenCode is running: `opencode serve --port 4096`
- Check OpenCode has a configured AI provider

### "ERR_BLOCKED_BY_CLIENT" in browser console

- Ad blocker is blocking requests
- Whitelist `localhost:3456` in your ad blocker
- Or temporarily disable ad blocker on X.com

### Research folder is empty

- OpenCode creates output in its working directory
- Run OpenCode from where you want outputs: `cd ~/research && opencode serve`

### Extension not loading

- Make sure to load from `packages/chrome-extension/dist/` (not the source folder)
- Rebuild after changes: `pnpm extension:build`

### Sessions not appearing in OpenCode TUI

This is usually caused by multiple OpenCode instances or stale TUI sessions.

**Understanding the issue:**
- OpenCode sessions are bound to the directory where `opencode serve` runs
- The TUI only shows sessions for its current working directory
- If you have multiple OpenCode instances, port 4096 may be owned by a different instance
- Stale TUI instances won't show newly created sessions

**Diagnose:**

```bash
ps aux | grep -E "opencode$" | grep -v grep
lsof -i :4096 | grep LISTEN
```

**Check which instance owns port 4096:**

```bash
for pid in $(ps aux | grep -E "opencode$" | grep -v grep | awk '{print $2}'); do
  cwd=$(lsof -p $pid 2>/dev/null | grep " cwd " | awk '{print $NF}')
  serves=$(lsof -i :4096 -p $pid 2>/dev/null | grep -c LISTEN || echo 0)
  if [ "$serves" -gt 0 ]; then
    echo "PID $pid SERVES :4096 from $cwd"
  fi
done
```

**Fix:**

1. Close stale OpenCode instances: `kill <pid>`
2. Ensure only ONE OpenCode instance is running with `opencode serve --port 4096`
3. Start OpenCode from your desired research directory:
   ```bash
   cd ~/my-research-workspace
   opencode
   ```

**Best practice:** Use a dedicated directory for BuildFlow research sessions and always start OpenCode from there.

### Sessions exist but not visible

Sessions may exist in the database but not show in TUI if:
- The TUI was started before the sessions were created
- A different OpenCode instance created them

**Verify sessions exist via API:**

```bash
curl -s http://localhost:4096/session | python3 -c "
import json, sys
for s in json.load(sys.stdin):
    if 'Build & Learn' in s.get('title', ''):
        print(f\"{s['id']}: {s['title'][:50]}...\")
"
```

If sessions appear in the API but not TUI, restart your TUI instance.

---

## Project Structure

```
.
├── packages/
│   ├── shared/                 # Shared code
│   │   ├── src/
│   │   │   ├── types.ts        # TypeScript interfaces
│   │   │   ├── prompts.ts      # Prompt generation
│   │   │   ├── utils.ts        # Utility functions
│   │   │   └── index.ts        # Exports
│   │   └── package.json
│   │
│   ├── chrome-extension/       # Browser extension
│   │   ├── src/
│   │   │   ├── popup/          # Extension popup
│   │   │   ├── content/        # X.com content script
│   │   │   └── background/     # Service worker
│   │   ├── manifest.json
│   │   └── package.json
│   │
│   ├── telegram-bot/           # Telegram integration
│   │   ├── src/
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   └── backend/                # API server
│       ├── src/
│       │   ├── routes/         # API routes
│       │   ├── services/       # Business logic
│       │   └── index.ts
│       ├── research/           # (SQLite DB location)
│       └── package.json
│
├── .opencode/
│   └── agent/
│       └── research-builder.md # Custom OpenCode agent
│
├── package.json                # Root workspace config
├── pnpm-workspace.yaml
├── tsconfig.base.json
└── README.md
```

---

## License

MIT

---

## Author

**BowTiedSwan** - [@BowTiedSwan](https://x.com/BowTiedSwan)

---

## Credits

Built to work with [OpenCode](https://opencode.ai) - the AI-powered coding assistant.

---

## Support

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/W7W31RTLJ6)
