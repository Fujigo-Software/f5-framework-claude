# Installation Guide

Hướng dẫn cài đặt F5 Framework cho Claude Code.

## Requirements

- Node.js >= 18
- pnpm >= 8 (hoặc npm >= 9)
- Git
- Claude Code CLI (`claude`)

## Installation Steps

### Step 1: Clone & Build

```bash
# Clone repository
git clone https://github.com/Fujigo-Software/f5-framework-claude.git
cd f5-framework

# Install dependencies
pnpm install

# Build
pnpm build

# Link CLI globally
cd packages/cli
npm link
```

### Step 2: Install Commands to Your Project

```bash
# Go to your project
cd your-project

# Install F5 commands
f5 install-commands

# Verify installation
f5 doctor
```

### Step 3: Setup MCP Servers (Recommended)

F5 Framework works best with these MCP servers. Add to your Claude Code settings (`~/.claude/settings.json`):

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-context7"]
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-sequential-thinking"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-playwright"]
    },
    "serena": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-serena"]
    }
  }
}
```

### MCP Server Purposes

| Server | Purpose | Used By |
|--------|---------|---------|
| **Context7** | Documentation lookup | `/f5-research`, `/f5-implement` |
| **Sequential** | Multi-step reasoning | `/f5-test-it`, `/f5-analyze` |
| **Playwright** | E2E & Visual testing | `/f5-test-e2e`, `/f5-test-visual` |
| **Serena** | Code understanding | `/f5-load`, `/f5-analyze` |

## What's Installed

When you run `f5 install-commands`, your project receives:

```
your-project/
├── .claude/
│   └── commands/         # 53 slash commands
└── ... (your files)
```

When you run `/f5-init`, F5 creates:

```
your-project/
├── .f5/
│   ├── config.json       # Project configuration
│   ├── gates.yaml        # Quality gate definitions
│   ├── personas.yaml     # AI persona settings
│   └── memory/           # Session state
└── ... (your files)
```

---

## Usage

### For New Projects

```bash
# Open Claude Code in your project
cd your-project
claude

# Initialize F5
/f5-init

# Load project (auto-detect stack)
/f5-load

# Verify everything is working
/f5-doctor

# Start working!
/f5-status
/f5-suggest
```

### For Existing Projects

```bash
cd your-existing-project
claude

# Load F5 (auto-detect stack)
/f5-load

# Verify setup
/f5-doctor

# Check current status
/f5-status
```

---

## Update F5 Framework

```bash
# Go to f5-framework directory
cd f5-framework

# Pull latest changes
git pull origin main

# Rebuild
pnpm install
pnpm build

# Re-link CLI
cd packages/cli
npm link

# In your project: reinstall commands
cd your-project
f5 install-commands

# Verify
f5 doctor
```

---

## Troubleshooting

### Permission Error

```bash
# macOS/Linux - use nvm
nvm install 18
nvm use 18

# Or fix npm permissions
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
```

### Commands Not Recognized

```bash
# Verify commands installed
ls .claude/commands/ | wc -l
# Should show 53 files

# Reinstall commands
f5 install-commands
```

### Version Mismatch

```bash
# In f5-framework folder, update
git pull origin main
pnpm install && pnpm build

# Re-link
cd packages/cli && npm link

# In project
f5 install-commands
/f5-doctor
```

### MCP Servers Not Working

```bash
# Check Claude settings file
cat ~/.claude/settings.json

# Verify MCP config exists
# Restart Claude Code after adding MCP config
```

---

## Next Steps

- [Quick Start](quick-start.md)
- [MCP Setup](mcp-setup.md)
- [First Project](first-project.md)
