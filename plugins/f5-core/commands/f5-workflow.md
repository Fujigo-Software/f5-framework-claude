---
description: Workflow management - load, status, next, help
argument-hint: <load|status|next|help> [workflow-name]
---

# /f5-workflow

## ARGUMENTS: $ARGUMENTS

---

# 🚨 MANDATORY FIRST STEP - READ THIS BEFORE DOING ANYTHING

**STOP! Parse arguments FIRST:**

Arguments received: `$ARGUMENTS`

**Step 1: Extract COMMAND and WORKFLOW_NAME from arguments**

```
If arguments = "load enterprise-app"  → COMMAND="load", WORKFLOW_NAME="enterprise-app"
If arguments = "load greenfield"  → COMMAND="load", WORKFLOW_NAME="greenfield"
If arguments = "load mvp"         → COMMAND="load", WORKFLOW_NAME="mvp"
If arguments = "load"             → COMMAND="load", WORKFLOW_NAME=null
If arguments = "status"           → COMMAND="status"
If arguments = "help"             → COMMAND="help"
If arguments = "next"             → COMMAND="next"
If arguments = "list"             → COMMAND="list"
```

**Step 2: Route based on parsed values**

| Condition | Action |
|-----------|--------|
| COMMAND="load" AND WORKFLOW_NAME is NOT empty | **SKIP EVERYTHING BELOW.** Go to section `[DIRECT-LOAD]` immediately. Do NOT analyze project. Do NOT ask questions. |
| COMMAND="load" AND WORKFLOW_NAME is empty | Go to section `[SMART-MODE]` |
| COMMAND="status" | Go to section `[STATUS]` |
| COMMAND="help" | Go to section `[HELP]` |
| COMMAND="next" | Go to section `[NEXT]` |
| COMMAND="list" | Go to section `[LIST]` |

---

# [DIRECT-LOAD]

**You are here because user provided a workflow name, e.g., `/f5-workflow load enterprise-app`**

**CRITICAL RULES:**
- ❌ Do NOT analyze project
- ❌ Do NOT ask questions about goals
- ❌ Do NOT recommend alternative workflows
- ✅ Just load the specified workflow template

## Step 1: Find Workflow Template

Look for workflow template in F5 Framework CLI:

```
Path: {PROJECT_FOLDER}\workflows\{WORKFLOW_NAME}\

Required files:
- WORKFLOW.md
- README.md
- phases/ folder
- commands/ folder (optional)
- checklists/ folder (optional)
- templates/ folder (optional)
```

If workflow NOT found, show error and list available workflows from `{PROJECT_FOLDER}\workflows\` folder.

## Step 2: Create Folder Structure

Create all folders in target project:

```powershell
# Run these commands in target project directory

# Input folders
New-Item -ItemType Directory -Force -Path ".f5\input\raw"
New-Item -ItemType Directory -Force -Path ".f5\input\classified"
New-Item -ItemType Directory -Force -Path ".f5\input\translated"
New-Item -ItemType Directory -Force -Path ".f5\input\change-requests"
New-Item -ItemType Directory -Force -Path ".f5\input\bugs"

# Specs folders
New-Item -ItemType Directory -Force -Path ".f5\specs\srs\v1.0.0\use-cases"
New-Item -ItemType Directory -Force -Path ".f5\specs\srs\v1.0.0\business-rules"
New-Item -ItemType Directory -Force -Path ".f5\specs\basic-design\v1.0.0\architecture"
New-Item -ItemType Directory -Force -Path ".f5\specs\basic-design\v1.0.0\screens"
New-Item -ItemType Directory -Force -Path ".f5\specs\basic-design\v1.0.0\api"
New-Item -ItemType Directory -Force -Path ".f5\specs\basic-design\v1.0.0\database"
New-Item -ItemType Directory -Force -Path ".f5\specs\detail-design\v1.0.0\screens"
New-Item -ItemType Directory -Force -Path ".f5\specs\detail-design\v1.0.0\api"
New-Item -ItemType Directory -Force -Path ".f5\specs\detail-design\v1.0.0\batch"
New-Item -ItemType Directory -Force -Path ".f5\specs\technical-design\v1.0.0\api-contracts"
New-Item -ItemType Directory -Force -Path ".f5\specs\technical-design\v1.0.0\data-models"
New-Item -ItemType Directory -Force -Path ".f5\specs\technical-design\v1.0.0\logic-flows"
New-Item -ItemType Directory -Force -Path ".f5\specs\technical-design\v1.0.0\error-handling"
New-Item -ItemType Directory -Force -Path ".f5\specs\technical-design\v1.0.0\_diffs"
New-Item -ItemType Directory -Force -Path ".f5\specs\technical-design\v1.0.0\_audit"

# Other folders
New-Item -ItemType Directory -Force -Path ".f5\questions\open"
New-Item -ItemType Directory -Force -Path ".f5\questions\resolved"
New-Item -ItemType Directory -Force -Path ".f5\quality"
New-Item -ItemType Directory -Force -Path ".f5\workflow"
New-Item -ItemType Directory -Force -Path ".f5\memory\checkpoints"
New-Item -ItemType Directory -Force -Path ".f5\knowledge"
New-Item -ItemType Directory -Force -Path ".f5\testing\baselines"
New-Item -ItemType Directory -Force -Path ".f5\debt"
New-Item -ItemType Directory -Force -Path ".f5\templates"

# Claude folders
New-Item -ItemType Directory -Force -Path ".claude\commands"
New-Item -ItemType Directory -Force -Path ".claude\workflows\phases"
```

## Step 3: Create Config Files

### 3.1 Create `.f5/config.json`

```json
{
  "project": {
    "name": "{CURRENT_PROJECT_FOLDER_NAME}",
    "stack": ".NET Framework 4.8 + WPF + Oracle",
    "language": {
      "customer": "Japanese",
      "team": "Vietnamese"
    }
  },
  "workflow": {
    "name": "{WORKFLOW_NAME}",
    "version": "3.1",
    "startedAt": "{TODAY_DATE}",
    "currentPhase": "input",
    "phaseIndex": 0,
    "phases": [
      {"number": 1, "name": "input", "status": "in_progress", "gate": "D1"},
      {"number": 2, "name": "specs", "status": "pending", "gate": "D2"},
      {"number": 3, "name": "design", "status": "pending", "gate": "D3,D4"},
      {"number": 4, "name": "technical-design", "status": "pending", "gate": "D5"},
      {"number": 5, "name": "implement", "status": "pending", "gate": "G2"},
      {"number": 6, "name": "test", "status": "pending", "gate": "G3"},
      {"number": 7, "name": "deploy", "status": "pending", "gate": "G4"}
    ],
    "gates": {
      "D1": {"status": "pending", "approver": "TechLead"},
      "D2": {"status": "pending", "approver": "Customer"},
      "D3": {"status": "pending", "approver": "Customer"},
      "D4": {"status": "pending", "approver": "TechLead"},
      "D5": {"status": "pending", "approver": "TechLead"},
      "G2": {"status": "pending", "approver": "TechLead"},
      "G3": {"status": "pending", "approver": "TechLead"},
      "G4": {"status": "pending", "approver": "Customer"}
    }
  }
}
```

### 3.2 Create `.f5/workflow/current-phase.yaml`

```yaml
phase:
  number: 1
  name: input
  status: in_progress
  startedAt: "{TODAY_DATE}"
  gate: D1
```

### 3.3 Create `.f5/quality/gates-status.yaml`

```yaml
gates:
  D1:
    status: pending
    approver: TechLead
    description: "Input classified & imported"
  D2:
    status: pending
    approver: Customer
    description: "SRS approved"
  D3:
    status: pending
    approver: Customer
    description: "Basic Design approved"
  D4:
    status: pending
    approver: TechLead
    description: "Detail Design approved"
  D5:
    status: pending
    approver: TechLead
    description: "Technical Design approved"
  G2:
    status: pending
    approver: TechLead
    description: "Code quality passed"
  G3:
    status: pending
    approver: TechLead
    description: "All tests passed"
  G4:
    status: pending
    approver: Customer
    description: "Ready for production"
```

### 3.4 Create `.f5/questions/_index.yaml`

```yaml
questions:
  total: 0
  open: 0
  resolved: 0
  items: []
```

### 3.5 Create other YAML files

Create these empty structure files:
- `.f5/knowledge/glossary.yaml`
- `.f5/testing/config.yaml`
- `.f5/debt/backlog.yaml`

## Step 4: Capability Check (NEW in V3.4)

### 4.1 Scan Input Folder

Scan input folder to detect file types:

```powershell
# Scan .f5/input/raw/ for file types
Get-ChildItem -Path ".f5\input\raw" -Recurse -File | Group-Object Extension | Select-Object Name, Count
```

### 4.2 Check AI Capabilities vs Input Files

Load capability matrix from workflow config:

```yaml
# From capability-check.yaml
capabilities:
  excel_content:
    name: "Excel Content Read"
    supported: false
    required_tool: "MCP Excel Server"
    fallback: "structure_only"
    file_patterns: ["*.xlsx", "*.xls"]
    
  pdf_text:
    name: "PDF Text Extract"
    supported: true
    
  markdown:
    name: "Markdown Process"
    supported: true
    
  csv:
    name: "CSV Process"
    supported: true
```

### 4.3 Check MCP Connections (Optional)

```powershell
# Check if MCP Excel Server is available
# This is optional - workflow can proceed without it
```

### 4.4 Generate Capability Report

Include in output:

```
CAPABILITY CHECK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| Capability | Status | Impact | Workaround |
|------------|--------|--------|------------|
| Excel content read | ⚠️ Limited | Cannot extract cell data | MCP Excel or manual |
| PDF text extract | ✅ Available | - | - |
| Markdown process | ✅ Available | - | - |
| CSV process | ✅ Available | - | - |
| MCP Excel Server | ❌ Not connected | Excel limitation | Connect MCP |

⚠️ WARNINGS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ W-001: Excel Binary Limitation
    Input folder chứa {N} files .xlsx
    AI không thể đọc cell content trực tiếp
    
    Options:
    → Connect MCP Excel Server (recommended)
    → Export Excel → CSV trước khi classify
    → Manual verification khi cần chi tiết

INPUT FOLDER SCAN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Path: .f5/input/raw/
  
  | Type | Count | Can Process |
  |------|-------|-------------|
  | .xlsx | {N} | ⚠️ Structure only |
  | .pdf | {N} | ✅ Full |
  | .md | {N} | ✅ Full |
  | .csv | {N} | ✅ Full |
```

## Step 5: Copy Workflow Assets

Copy from workflow template to target project:

| Source | Destination |
|--------|-------------|
| `{WORKFLOW_PATH}/commands/*.md` | `.claude/commands/` |
| `{WORKFLOW_PATH}/checklists/*` | `.f5/quality/` |
| `{WORKFLOW_PATH}/templates/*` | `.f5/templates/` |
| `{WORKFLOW_PATH}/WORKFLOW.md` | `.claude/workflows/WORKFLOW.md` |
| `{WORKFLOW_PATH}/phases/*.md` | `.claude/workflows/phases/` |

## Step 6: Show Success Message

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  ✅ WORKFLOW LOADED: {WORKFLOW_NAME}                                      ║
╠═══════════════════════════════════════════════════════════════════════════╣

WORKFLOW INFO:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Template:    {WORKFLOW_NAME}
  Version:     3.4
  Phases:      7 (Input → Classify → Specs → Design → Implement → Test → Deploy)

CAPABILITY CHECK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| Capability | Status | Impact | Workaround |
|------------|--------|--------|------------|
| Excel content read | ⚠️ Limited | Cannot extract cell data | MCP Excel or manual |
| PDF text extract | ✅ Available | - | - |
| Markdown process | ✅ Available | - | - |
| CSV process | ✅ Available | - | - |
| MCP Excel Server | ❌ Not connected | Excel limitation | Connect MCP |

⚠️ WARNINGS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ W-001: Excel Binary Limitation
    Input folder chứa {N} files .xlsx
    AI không thể đọc cell content trực tiếp
    
    Options:
    → Connect MCP Excel Server (recommended)
    → Export Excel → CSV trước khi classify
    → Manual verification khi cần chi tiết

INPUT FOLDER SCAN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Path: .f5/input/raw/
  
  | Type | Count | Can Process |
  |------|-------|-------------|
  | .xlsx | {N} | ⚠️ Structure only |
  | .pdf | {N} | ✅ Full |
  | .md | {N} | ✅ Full |
  | .csv | {N} | ✅ Full |

FOLDER STRUCTURE CREATED:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

├── ✅ .f5/input/ (raw, classified, change-requests, bugs)
├── ✅ .f5/specs/ (srs, basic-design, detail-design, technical-design)
├── ✅ .f5/questions/ (open, resolved)
├── ✅ .f5/quality/
├── ✅ .f5/workflow/
├── ✅ .f5/memory/
├── ✅ .f5/knowledge/
├── ✅ .f5/testing/
├── ✅ .f5/debt/
└── ✅ .f5/templates/

CONFIG FILES CREATED:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

├── ✅ .f5/config.json
├── ✅ .f5/workflow/current-phase.yaml
├── ✅ .f5/workflow/capability-check.yaml  (NEW)
├── ✅ .f5/quality/gates-status.yaml
└── ✅ .f5/questions/_index.yaml

ASSETS COPIED:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

├── ✅ Commands → .claude/commands/
├── ✅ Checklists → .f5/quality/
├── ✅ Templates → .f5/templates/
└── ✅ Workflow → .claude/workflows/

╠═══════════════════════════════════════════════════════════════════════════╣

Current State:
┌─────────────────────────────────────────────────────────────────────┐
│ Workflow        │ {WORKFLOW_NAME}                                   │
│ Version         │ 3.4                                               │
│ Current Phase   │ 1 - Input                                         │
│ Next Gate       │ D1                                                │
└─────────────────────────────────────────────────────────────────────┘

Phase Progress:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
→ Input    ○ Specs    ○ Design    ○ Tech-Design    ○ Implement    ○ Test    ○ Deploy
   D1        D2        D3/D4         D5               G2            G3         G4
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RECOMMENDATIONS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  1. (Optional) Connect MCP Excel Server để đọc Excel content
  2. (Optional) Export key Excel files → CSV
  3. Proceed với awareness về limitations

NEXT STEPS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  1. Place input files in .f5/input/raw/
  2. Run /f5-classify <path> to classify files
  3. Complete D1 gate: /f5-gate check D1

╚═══════════════════════════════════════════════════════════════════════════╝
```

**STOP HERE. Do not continue to other sections.**

---

# [SMART-MODE]

**You are here because user ran `/f5-workflow load` WITHOUT workflow name**

Analyze project and recommend workflow:

1. Check if project has existing code
2. Check if project has documents
3. Ask 2-3 simple questions about goal
4. Recommend workflow with reasoning
5. If user accepts, go to [DIRECT-LOAD] with recommended workflow name

---

# [STATUS]

Show current workflow status from `.f5/config.json`

---

# [HELP]

Show help for current phase from `.claude/workflows/phases/`

---

# [NEXT]

Advance to next phase with gate validation

---

# [LIST]

List all available workflows from `{PROJECT_FOLDER}\workflows\` folder
