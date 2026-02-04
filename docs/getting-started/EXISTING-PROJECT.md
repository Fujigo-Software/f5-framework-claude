# Apply F5 Framework to Existing Project

This guide shows you how to add F5 Framework to your existing project, whether it's a React app, Node.js backend, or any other codebase.

## Overview

F5 Framework works by adding configuration files to your project. It doesn't modify your existing code - it just provides intelligent assistance through Claude Code.

## What F5 Adds to Your Project

```
your-project/
├── .f5/                         # F5 configuration
│   ├── config.json              # Project settings
│   ├── memory/                  # Session state
│   │   ├── constitution.md      # Project rules
│   │   ├── session.md           # Current context
│   │   ├── decisions.md         # Architecture decisions
│   │   └── learnings.md         # Accumulated knowledge
│   └── workflow-integration/    # Workflow features
├── .claude/                     # Claude Code integration
│   └── commands/                # 53 slash commands
├── CLAUDE.md                    # Project instructions (optional)
└── ... (your existing files)
```

## Step-by-Step Guide

### 1. Install F5 CLI (One-time Setup)

```bash
# Clone F5 Framework
git clone https://github.com/Fujigo-Software/f5-framework-claude.git
cd f5-framework

# Install dependencies and build
pnpm install
pnpm build

# Link CLI globally
cd packages/cli
npm link
```

### 2. Install Commands to Your Project

```bash
# Go to your project
cd your-existing-project

# Install F5 commands
f5 install-commands

# Verify installation
f5 doctor
```

### 3. Setup MCP Servers (Recommended)

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

### 4. Initialize F5 in Claude Code

```bash
# Open Claude Code
cd your-existing-project
claude

# Initialize F5
/f5-init

# Load project (auto-detect stack)
/f5-load

# Verify everything is working
/f5-doctor
```

### 5. Choose Your Workflow

F5 will auto-detect your project type, but you can explicitly choose:

```bash
# List available workflows
/f5-workflow list

# For adding features
/f5-workflow select feature-development

# For bug fixes
/f5-workflow select maintenance

# For major changes
/f5-workflow select legacy-migration
```

### 6. Configure for Your Stack

F5 adapts to your technology stack:

**For React/Next.js Projects:**
```bash
/f5-mode frontend
/f5-persona frontend
/f5-verbosity normal
```

**For Node.js/Express/NestJS:**
```bash
/f5-mode backend
/f5-persona backend
/f5-verbosity normal
```

**For Full-Stack Projects:**
```bash
/f5-mode coding
/f5-persona architect
/f5-verbosity detailed
```

**For Python/Django/FastAPI:**
```bash
/f5-mode backend
/f5-persona backend
/f5-verbosity normal
```

### 7. (Optional) Create CLAUDE.md

Add a `CLAUDE.md` to your project root for project-specific instructions:

```markdown
# Project Instructions

## F5 Framework Integration
This project uses F5 Framework for development workflow.

### Quick Commands
- `/f5-status` - Check current state
- `/f5-workflow` - Manage workflow
- `/f5-mode` - Switch development mode
- `/f5-suggest` - Get contextual suggestions

### Current Configuration
- Workflow: feature-development
- Mode: coding
- Verbosity: normal

## Project-Specific Rules

### Code Style
- Use TypeScript strict mode
- Follow existing naming conventions
- Maximum file size: 300 lines

### Testing
- Unit tests required for all services
- Minimum coverage: 80%

### Architecture
- Follow existing folder structure
- Use dependency injection
- No direct database calls from controllers

## Important Files
- `src/config/` - Configuration files
- `src/services/` - Business logic
- `src/controllers/` - API endpoints
- `tests/` - Test files
```

## Common Scenarios

### Scenario 1: Adding a New Feature to React Project

```bash
# Start Claude Code
cd my-react-app
claude

# Initialize F5
/f5-init

# Select feature development workflow
/f5-workflow select feature-development

# Set frontend mode
/f5-mode frontend

# Verify configuration
/f5-doctor

# Get suggestions
/f5-suggest

# Start implementing
/f5-implement start FEAT-001
```

### Scenario 2: Fixing Bugs in Node.js Backend

```bash
cd my-node-api
claude

# Initialize or load F5
/f5-init

# Verify configuration
/f5-doctor

# Select maintenance workflow
/f5-workflow select maintenance

# Set debugging mode
/f5-mode debugging

# Analyze the bug
/f5-bug analyze BUG-123

# Fix with regression test
/f5-implement fix BUG-123
```

### Scenario 3: Refactoring Legacy Code

```bash
cd legacy-project
claude

/f5-init

# Verify setup
/f5-doctor

# Select maintenance or legacy-migration
/f5-workflow select maintenance

# Set analytical mode first
/f5-mode analytical

# Analyze impact
/f5-analyze impact --scope module

# Then switch to coding
/f5-mode coding
```

### Scenario 4: Building MVP Fast

```bash
cd my-startup
claude

/f5-init

# Verify
/f5-doctor

# Select MVP workflow
/f5-workflow select mvp

# Rapid development mode
/f5-mode coding
/f5-verbosity minimal

# Focus on essentials
/f5-ba prioritize --moscow
```

## Preserving Your Existing Setup

F5 is designed to work alongside your existing tools:

### With ESLint/Prettier
F5 respects your existing linting rules. Add to `.f5/memory/constitution.md`:
```markdown
## Code Style
- Follow existing ESLint configuration
- Run `npm run lint` before committing
```

### With Existing Tests
F5 works with any testing framework. Add to constitution:
```markdown
## Testing
- Use existing test framework (Jest/Mocha/etc)
- Run `npm test` to verify changes
```

### With Git Hooks
F5 doesn't interfere with pre-commit hooks or CI/CD.

### With Existing Documentation
F5's docs are in `.f5/` folder, separate from your docs.

## File Permissions

After installation, ensure proper permissions:
```bash
# Make sure .claude/commands are readable
chmod -R 755 .claude/commands/

# Make .f5 writable for config updates
chmod -R 755 .f5/
```

## Gitignore Recommendations

Add to your `.gitignore`:
```gitignore
# F5 Framework - keep config, ignore temp
.f5/memory/session.md
.f5/output/
```

Keep these tracked:
```
# Track these F5 files
.f5/config.json
.f5/memory/constitution.md
.f5/memory/decisions.md
.f5/memory/learnings.md
.claude/commands/
```

## Verifying Installation

Run these commands to verify F5 is working:

```bash
# In terminal: check CLI installation
f5 doctor

# Should show:
# ✓ Commands: 53 files
# ✓ Assets: Synced
# ✓ CLI: Linked

# In Claude Code: verify configuration
/f5-doctor

# Should show:
# F5 Framework v1.2.x
# Status: Healthy
# Config: Found
# Commands: 53 loaded
```

## Troubleshooting

### "Command not found" Error
```bash
# Ensure commands installed
f5 install-commands

# Verify .claude/commands/ exists
ls .claude/commands/

# Should show 53 files
ls .claude/commands/ | wc -l

# Restart Claude Code
```

### "Configuration not loading"
```bash
# Check .f5/ folder
ls .f5/

# If missing config.json
/f5-init

# If corrupted, reset
rm -rf .f5/
/f5-init
```

### "Workflow not available"
```bash
# Check workflow-integration exists
ls .f5/workflow-integration/

# If missing, update
f5 install-commands
/f5-load --update
```

### "Skills not loading"
```bash
# List available skills
/f5-skill list

# Add specific skill
/f5-skill add api-design
```

## Removing F5 (If Needed)

To completely remove F5 from your project:
```bash
# Remove F5 folders
rm -rf .f5/
rm -rf .claude/

# Remove CLAUDE.md if F5-specific
rm CLAUDE.md

# Your code is unchanged
```

---

## Next Steps

- [Command Reference](../reference/commands.md) - All F5 commands
- [Skills Guide](guides/skills-guide.md) - Available skills
- [Workflows Guide](guides/workflows-guide.md) - Workflow details

**Need help?** Contact [Fujigo Software](https://fujigo-soft.com/en/contact.html)
