# 🧠 Claude Bilateral Memory

> Give Claude a persistent brain that works across both **Claude Code** and **Claude.ai**

By default, every Claude session starts fresh — it remembers nothing from previous conversations. This project solves that by creating a shared memory system that persists across sessions and bridges both Claude tools.

---

## The Problem

Every time you start a new Claude session, you repeat yourself:
- *"My project is built in Python"*
- *"My database is PostgreSQL"*
- *"Last time we were working on the login module"*

This is frustrating and wastes time. Claude Bilateral Memory fixes this.

---

## How It Works

```
Claude Code  ←→  MCP Server (mcp.js)  ←→  memory.json  ←→  REST API (api.js)  ←→  Claude.ai
```

| Component | What it does |
|---|---|
| `src/mcp.js` | MCP server — lets Claude Code read/write memories automatically |
| `src/api.js` | REST API bridge — lets you access memories from Claude.ai |
| `~/.claude-shared-memory.json` | The memory file stored on your computer |

Think of it like a **shared notebook**:
- **Claude Code** sits at your desk and can read/write the notebook anytime
- **Claude.ai** is a friend on the phone — you read the notebook to them by copying and pasting

---

## Requirements

| Tool | Purpose |
|---|---|
| [Node.js LTS](https://nodejs.org) | Runs the MCP server and API |
| [Claude Code](https://claude.ai/code) | The terminal-based Claude tool |
| [Git Bash](https://git-scm.com/downloads/win) | **Windows only** — required by Claude Code |

---

## Installation

### Step 1: Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/claude-bilateral-memory.git
cd claude-bilateral-memory
```

### Step 2: Install dependencies
```bash
npm install
```

### Step 3: Set up Git Bash path (Windows only)
Claude Code on Windows requires Git Bash. Set this permanently:
```bash
setx CLAUDE_CODE_GIT_BASH_PATH "C:\Users\YourName\AppData\Local\Programs\Git\bin\bash.exe"
```
Then close and reopen your terminal.

### Step 4: Register the MCP server with Claude Code

**Mac/Linux:**
```bash
claude mcp add --scope user shared-memory -- node /full/path/to/claude-bilateral-memory/src/mcp.js
```

**Windows:**
```bash
claude mcp add --scope user shared-memory -- node C:\full\path\to\claude-bilateral-memory\src\mcp.js
```

### Step 5: Verify the connection
Open Claude Code and type `/mcp` — you should see:
```
shared-memory · ✅ connected
```

---

## Daily Usage

### Every time you start working:

**Terminal 1 — Start Claude Code:**
```bash
claude
```

**Terminal 2 — Start the API bridge (only needed for Claude.ai access):**
```bash
node src/api.js
```

---

### Using memory in Claude Code

Just ask naturally — Claude Code handles everything automatically:

```
Save a memory: our project uses Python and Flask
```
```
What do you remember about our project?
```
```
List all memories
```
```
Delete the memory called "project"
```

---

### Using memory in Claude.ai

**1. Make sure `api.js` is running** (see above)

**2. Open your browser and visit:**
```
http://localhost:3001/memory
```

**3. Copy the output and paste it into Claude.ai:**
```
Here is my saved context, please use this:
{ "project": { "value": "Python and Flask app", ... } }
```

Claude.ai will now have full context of everything you've saved.

---

## API Reference

The REST API runs on `http://localhost:3001` by default.

### Get all memories
```bash
GET /memory

curl http://localhost:3001/memory
```

### Get a specific memory
```bash
GET /memory/:key

curl http://localhost:3001/memory/project
```

### Save a memory
```bash
POST /memory
Content-Type: application/json
{ "key": "project", "value": "Python and Flask app" }

curl -X POST http://localhost:3001/memory \
  -H "Content-Type: application/json" \
  -d '{"key": "project", "value": "Python and Flask app"}'
```

### Delete a memory
```bash
DELETE /memory/:key

curl -X DELETE http://localhost:3001/memory/project
```

---

## MCP Tools Reference

These tools are available inside Claude Code automatically:

| Tool | Description | Example |
|------|-------------|---------|
| `save_memory` | Save a key-value memory | *"Save that we use PostgreSQL"* |
| `get_memory` | Retrieve a memory by key | *"What do you remember about the database?"* |
| `list_memories` | List all stored memories | *"List everything you remember"* |
| `delete_memory` | Delete a memory by key | *"Forget the database memory"* |

---

## Memory File

All memories are stored locally in a JSON file on your computer:

| OS | Location |
|---|---|
| Mac/Linux | `~/.claude-shared-memory.json` |
| Windows | `C:\Users\YourName\.claude-shared-memory.json` |

Example memory file:
```json
{
  "project": {
    "value": "Python and Flask app",
    "timestamp": "2026-04-01T06:00:00.000Z",
    "source": "claude-code"
  },
  "database": {
    "value": "PostgreSQL on port 5432",
    "timestamp": "2026-04-01T07:00:00.000Z",
    "source": "claude-code"
  }
}
```

---

## Project Structure

```
claude-bilateral-memory/
├── src/
│   ├── mcp.js        # MCP server for Claude Code
│   └── api.js        # REST API bridge for Claude.ai
├── package.json
├── .gitignore
└── README.md
```

---

## Troubleshooting

### `claude` is not recognized
```bash
npm install -g @anthropic-ai/claude-code
```

### Claude Code says Git Bash is required (Windows)
```bash
setx CLAUDE_CODE_GIT_BASH_PATH "C:\Users\YourName\AppData\Local\Programs\Git\bin\bash.exe"
```
Close and reopen your terminal, then try again.

### MCP server shows ✗ Failed to connect
```bash
# Test the file directly
node src/mcp.js

# Reinstall dependencies
npm install
```

### Environment variable not saving (Windows)
Use `setx` instead of `set`:
```bash
# WRONG - temporary only
set CLAUDE_CODE_GIT_BASH_PATH=C:\path\to\bash.exe

# CORRECT - permanent
setx CLAUDE_CODE_GIT_BASH_PATH "C:\path\to\bash.exe"
```

---

## Limitations

- Claude.ai does not natively support MCP, so memory access requires a manual copy/paste step
- The `api.js` server must be running for Claude.ai access
- Memories are stored locally — not synced to the cloud

---

## Why This Is Useful

| Without This | With This |
|---|---|
| Repeat context every session | Say it once, remembered forever |
| Claude Code and Claude.ai are isolated | Both share the same knowledge |
| Start from scratch each time | Pick up exactly where you left off |
| Manually explain your stack every time | Claude already knows your setup |

---

## License

MIT
