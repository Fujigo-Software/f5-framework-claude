# Getting Started with F5 Framework

Welcome to F5 Framework! This guide will help you get up and running in 5 minutes.

## Prerequisites

- **Node.js** >= 18 with pnpm >= 8
- **Git** installed
- **Claude Code CLI** installed ([install guide](https://docs.anthropic.com/claude-code))
- An existing project or new project folder

## Quick Start (5 minutes)

### Step 1: Clone & Build F5 Framework

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

Add to `~/.claude/settings.json`:

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

### Step 4: Initialize F5 in Your Project

```bash
# Open Claude Code in your project
cd your-project
claude

# Initialize F5
/f5-init

# Load project (auto-detect stack + 2 questions)
/f5-load

# Verify everything is working
/f5-doctor
```

### Step 5: Start Working

```bash
# Check current status
/f5-status

# Get contextual suggestions
/f5-suggest

# Switch mode if needed
/f5-mode architect
```

## Workflow Selection Guide

Choose the right workflow for your project type:

| Project Type | Workflow | Description |
|--------------|----------|-------------|
| **Startup/MVP** | `mvp` | Fast iteration, lean approach |
| **New Enterprise** | `greenfield` | Full architecture planning |
| **Add Feature** | `feature-development` | Focused scope, existing codebase |
| **Bug Fix/Refactor** | `maintenance` | Stability focus, minimal changes |
| **Legacy System** | `legacy-migration` | Safe incremental modernization |
| **Cloud Move** | `cloud-migration` | Infrastructure-focused |
| **Quick Prototype** | `poc` | Rapid validation, throwaway code |

```bash
# Select a workflow
/f5-workflow load mvp

# Get workflow guidance
/f5-workflow help
```

## First Session Checklist

- [ ] Run `f5 install-commands` in your project
- [ ] Run `f5 doctor` to verify installation
- [ ] Run `/f5-init` to initialize F5
- [ ] Run `/f5-load` to load project context
- [ ] Run `/f5-doctor` to verify configuration
- [ ] Select workflow with `/f5-workflow select <name>`
- [ ] Set mode with `/f5-mode <mode>`
- [ ] Check status with `/f5-status`
- [ ] Get suggestions with `/f5-suggest`

## Understanding F5 Modes

F5 has different **modes** that change how Claude thinks:

| Mode | When to Use |
|------|-------------|
| `analytical` | Understanding requirements, research |
| `planning` | Architecture, design decisions |
| `coding` | Writing code, implementation |
| `debugging` | Bug fixing, troubleshooting |
| `security` | Security review, vulnerability checks |

Switch modes with:
```bash
/f5-mode analytical   # For analysis
/f5-mode coding       # For implementation
/f5-mode debugging    # For bug fixing
```

## Understanding F5 Personas

**Personas** bring domain expertise:

| Persona | Expertise |
|---------|-----------|
| `analyst` | Business requirements, user stories |
| `architect` | System design, patterns |
| `backend` | APIs, databases, services |
| `frontend` | UI/UX, components, accessibility |
| `qa` | Testing, quality assurance |
| `devops` | CI/CD, deployment, infrastructure |
| `security` | Security best practices |

Switch personas with:
```bash
/f5-persona architect   # For system design
/f5-persona backend     # For API work
/f5-persona qa          # For testing
```

## Essential Commands

| Command | Description |
|---------|-------------|
| `/f5-init` | Initialize F5 for new project |
| `/f5-load` | Load F5 for existing project |
| `/f5-doctor` | Health check - run after init/load |
| `/f5-status` | Show current configuration |
| `/f5-workflow` | Manage development workflow |
| `/f5-mode` | Switch development mode |
| `/f5-persona` | Activate specialized expertise |
| `/f5-suggest` | Get contextual suggestions |
| `/f5-gate` | Check quality gates |
| `/f5-help` | Show all commands |

## Quality Gates

F5 uses quality gates to ensure you're ready before proceeding:

| Gate | Name | Description |
|------|------|-------------|
| D1 | Research | Requirements understood |
| D2 | SRS | Specification approved |
| D3 | Basic Design | Architecture approved |
| D4 | Detail Design | Full design complete |
| G2 | Implementation | Code complete |
| **G2.5** | **Verification** | **Assets & integration verified** |
| G3 | Testing | Tests passing + visual regression |
| G4 | Deployment | Ready for production |

Check gate status:
```bash
/f5-gate status
/f5-gate check D1
```

## What's Next?

1. **Apply to existing project**: [EXISTING-PROJECT.md](./EXISTING-PROJECT.md)
2. **Full command reference**: [Command Reference](../reference/commands.md)
3. **Skills guide**: [guides/skills-guide.md](guides/skills-guide.md)
4. **Domains guide**: [guides/domains-guide.md](guides/domains-guide.md)

## Troubleshooting

### "Command not found"
```bash
# Ensure F5 commands are installed
f5 install-commands

# Verify commands
ls .claude/commands/ | wc -l
# Should show 53 files

# Restart Claude Code
```

### "Configuration not loading"
```bash
# Check .f5/ folder exists
ls .f5/

# Re-initialize if needed
/f5-init

# Verify with doctor
/f5-doctor
```

### "Workflow not available"
```bash
# List available workflows
/f5-workflow list

# Update assets
f5 install-commands
/f5-load --update
```

---

**Need help?** Contact [Fujigo Software](https://fujigo-soft.com/en/contact.html)
