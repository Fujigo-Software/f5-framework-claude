# F5 Framework Documentation

> **Version:** 1.3.6 | **Updated:** January 2025

---

## Documentation Structure

```
docs/
├── getting-started/    # Installation and setup guides
├── guides/             # Workflow and feature guides
├── mcp/                # MCP server documentation
├── testing/            # Testing documentation
├── integrations/       # External integrations
├── reference/          # Command and API reference
├── releases/           # Migration and changelog
├── agents/             # Agent documentation
├── cli/                # CLI documentation
└── domains/            # Domain templates
```

---

## Getting Started

| Document | Description |
|----------|-------------|
| [Getting Started Index](./getting-started/INDEX.md) | Start here |
| [Quick Start](./getting-started/QUICK-START.md) | Get up and running |
| [Installation](./getting-started/installation.md) | Detailed installation |
| [Existing Project](./getting-started/EXISTING-PROJECT.md) | Apply to existing codebase |
| [F5 Workflow Studio](./getting-started/F5-WF-STUDIO.md) | Visual workflow editor |
| [MCP Setup](./getting-started/mcp-setup.md) | Configure MCP servers |

---

## Guides

| Document | Description |
|----------|-------------|
| [Quality Gates](./guides/quality-gates.md) | D1-D4, G2-G4 gate system |
| [Strict Mode](./guides/strict-mode.md) | Strict implementation protocol |
| [BA Workflow](./guides/ba-workflow.md) | Business analysis workflow |
| [Project Setup](./guides/project-setup.md) | Project configuration |
| [Context Config](./guides/context-config.md) | Context configuration |

---

## MCP (Model Context Protocol)

| Document | Description |
|----------|-------------|
| [MCP Index](./mcp/INDEX.md) | MCP onboarding guide |
| [MCP Architecture](./mcp/ARCHITECTURE.md) | Technical architecture |
| [Quick Reference](./mcp/QUICK-REFERENCE.md) | Quick reference card |
| [Setup Checklist](./mcp/SETUP-CHECKLIST.md) | Setup checklist |
| [MCP Servers](../mcp/README.md) | Server details |

### Quick Commands

```bash
/f5-mcp                # List servers
/f5-mcp profile X      # Switch profile
/f5-mcp health         # Health check
```

### Server Tiers

| Tier | Count | Examples |
|------|-------|----------|
| Core | 7 | context7, memory, playwright |
| Fujigo | 2 | redmine, docparse |
| Tier 1 | 4 | figma, postgres, sentry |
| Tier 2 | 3 | gitlab, linear, semgrep |

---

## Testing

| Document | Description |
|----------|-------------|
| [Testing Index](./testing/INDEX.md) | Testing documentation hub |
| [Testing README](./testing/README.md) | Main testing guide |
| [Test Guidelines](./testing/GUIDELINES.md) | Test writing guidelines |
| [Phase 1 Testing](./testing/PHASE1.md) | Testing commands optimization |
| [Pre-commit Hooks](./testing/PRE-COMMIT.md) | Pre-commit configuration |

---

## Integrations

| Document | Description |
|----------|-------------|
| [Jira Integration](./integrations/jira.md) | Jira sync and import |
| [Excel Schema](./integrations/excel-schema.md) | Excel import format |
| [MCP Excel](./integrations/mcp-excel.md) | MCP Excel integration |
| [Document Pipeline](./integrations/document-pipeline.md) | Document processing |

---

## Reference

| Document | Description |
|----------|-------------|
| [Command Reference](./reference/commands.md) | All 82 slash commands |
| [CI/CD Setup](./reference/ci-cd.md) | CI/CD configuration |

---

## Releases

| Document | Description |
|----------|-------------|
| [Phase 2 Migration](./releases/PHASE2-MIGRATION.md) | v1.3.x migration guide |
| [v1.2.7 Migration](./releases/MIGRATION-1.2.7.md) | v1.2.7 migration notes |

---

## Quick Start

### Installation

```bash
# Clone and setup
git clone https://github.com/Fujigo-Software/f5-framework-claude.git f5-framework
cd f5-framework

# Build
pnpm install && pnpm build
cd packages/cli && npm link

# Install commands
f5 install-commands
```

### In Your Project

```bash
cd your-project
claude

# In Claude Code:
/f5-init    # New project
/f5-load    # Existing project
/f5-doctor  # Verify
```

---

## Support

- Create issue in repository
- Team Slack: #f5-framework
- See [FAQ](./mcp/INDEX.md#faq)

---

*F5 Framework Documentation v1.3.6*
