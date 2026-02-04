# Getting Started with F5 Framework

> **Version:** 1.3.6 | **Last Updated:** January 2025

---

## Quick Links

| Document | Description |
|----------|-------------|
| [Quick Start](./QUICK-START.md) | Get up and running in 5 minutes |
| [Installation](./installation.md) | Detailed installation guide |
| [Existing Project](./EXISTING-PROJECT.md) | Apply F5 to existing codebase |
| [F5 Workflow Studio](./F5-WF-STUDIO.md) | Visual workflow editor setup |
| [MCP Setup](./mcp-setup.md) | Configure MCP servers |
| [Clone Repo Guide](./quick-start-clone.md) | Quick start from cloned repo |

---

## Installation Options

### Option 1: Quick Start (Recommended)

```bash
# Clone F5 Framework
git clone https://github.com/Fujigo-Software/f5-framework-claude.git f5-framework
cd f5-framework

# Build and link
pnpm install && pnpm build
cd packages/cli && npm link

# Install commands
f5 install-commands
```

### Option 2: Using Cloned Repo

See [Clone Repo Guide](./quick-start-clone.md) for step-by-step instructions.

---

## First Steps After Installation

1. **Verify Installation**
   ```bash
   f5 doctor
   ```

2. **Initialize New Project**
   ```bash
   cd your-project
   claude
   /f5-init
   ```

3. **Load Existing Project**
   ```bash
   cd your-project
   claude
   /f5-load
   ```

---

## What's Next?

| Goal | Document |
|------|----------|
| Understand commands | [Command Reference](../reference/commands.md) |
| Configure MCP servers | [MCP Setup](./mcp-setup.md) |
| Learn quality gates | [Quality Gates Guide](../guides/quality-gates.md) |
| Set up testing | [Testing Documentation](../testing/INDEX.md) |

---

## Prerequisites

| Requirement | Version | Check |
|-------------|---------|-------|
| Node.js | >= 18.0.0 | `node --version` |
| pnpm | >= 8.0.0 | `pnpm --version` |
| Claude Code CLI | Latest | `claude --version` |
| Git | >= 2.30 | `git --version` |

---

*F5 Framework - Getting Started Index v1.3.6*
