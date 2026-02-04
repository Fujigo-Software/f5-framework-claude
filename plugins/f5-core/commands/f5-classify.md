---
description: Classify input files by type using content-first analysis, detect gaps, and generate coverage declaration
argument-hint: <path> [--dry-run] [--lang=vi|en|all] [review|approve]
---

# /f5-classify - Input Classification Command V3.0

Phân loại input files (Excel, documents) theo type, detect gaps, và tạo Coverage Declaration.

> **Version:** 3.0 (Content-First Classification)
> **Nguyên tắc:** AI support - Human decide
> **Thay đổi chính:** Phân loại dựa trên NỘI DUNG file thay vì tên file

---

## 🎯 VAI TRÒ CỦA F5-CLASSIFY

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  f5-classify KHÔNG PHẢI là bước tạo spec                                        │
│  f5-classify KHÔNG quyết định đúng/sai nội dung                                 │
├─────────────────────────────────────────────────────────────────────────────────┤
│  VAI TRÒ ĐÚNG:                                                                  │
│  ✅ Phân loại tài liệu theo type (dựa trên CONTENT)                             │
│  ✅ Trích xuất thông tin có cấu trúc                                            │
│  ✅ Khai báo độ bao phủ (Coverage Declaration)                                  │
│  ✅ Khai báo chất lượng (Classification Quality Level)                          │
│  ✅ Khai báo giới hạn kỹ thuật (Technical Limitations)                          │
│  ✅ Gắn cờ rủi ro (Missing Sections, Low Confidence)                            │
│  ✅ Chuẩn hoá input cho các phase phía sau                                      │
├─────────────────────────────────────────────────────────────────────────────────┤
│  QUYẾT ĐỊNH CUỐI CÙNG: BA / DEV / Tech Lead                                     │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🆕 WHAT'S NEW IN V3.0

| Feature | V2.1 | V3.0 |
|---------|------|------|
| Classification method | Name-based (tên file) | **Content-first** (nội dung) |
| Accuracy | Phụ thuộc naming convention | Dựa trên nội dung thực tế |
| Confidence | Based on name pattern match | Based on content analysis depth |
| Fallback | None | Name-based khi không đọc được content |

---

## 🧠 CLASSIFICATION LOGIC V3.0 - CONTENT-FIRST

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  CLASSIFICATION PRIORITY                                                  ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  PRIORITY 1: Content-based (ƯU TIÊN)                                      ║
║  ─────────────────────────────────────                                    ║
║  Đọc nội dung file → Phân tích → Xác định loại                           ║
║  Confidence: HIGH                                                         ║
║                                                                           ║
║  PRIORITY 2: Name-based (FALLBACK)                                        ║
║  ─────────────────────────────────                                        ║
║  Chỉ dùng khi KHÔNG đọc được content                                      ║
║  Confidence: LOW (cần human verification)                                 ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### CLASSIFICATION FLOW

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         CLASSIFICATION FLOW V3.0                                 │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  INPUT FILE                                                                      │
│      │                                                                           │
│      ▼                                                                           │
│  ┌─────────────────────────────┐                                                │
│  │ Có thể đọc content không?   │                                                │
│  │ (MCP Excel / CSV / PDF)     │                                                │
│  └─────────────────────────────┘                                                │
│      │                                                                           │
│  ┌───┴───┐                                                                       │
│  │       │                                                                       │
│ YES      NO                                                                      │
│  │       │                                                                       │
│  ▼       ▼                                                                       │
│ ┌─────────────────┐  ┌─────────────────┐                                        │
│ │ CONTENT-BASED   │  │ NAME-BASED      │                                        │
│ │ ANALYSIS        │  │ FALLBACK        │                                        │
│ │                 │  │                 │                                        │
│ │ 1. Read content │  │ 1. Parse name   │                                        │
│ │ 2. Analyze      │  │ 2. Match rules  │                                        │
│ │ 3. Determine    │  │ 3. Determine    │                                        │
│ │                 │  │                 │                                        │
│ │ Confidence:     │  │ Confidence:     │                                        │
│ │ HIGH/MEDIUM     │  │ LOW             │                                        │
│ └─────────────────┘  └─────────────────┘                                        │
│      │                    │                                                      │
│      └────────┬───────────┘                                                      │
│               ▼                                                                  │
│  ┌─────────────────────────────┐                                                │
│  │ OUTPUT: Classification      │                                                │
│  │ + Confidence Level          │                                                │
│  │ + Evidence (content/name)   │                                                │
│  └─────────────────────────────┘                                                │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📖 CONTENT ANALYSIS RULES

### Cách xác định loại tài liệu dựa trên NỘI DUNG:

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  CONTENT ANALYSIS RULES                                                   ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  DD (Detail Design) - Khi có:                                             ║
║  ─────────────────────────────                                            ║
║  • UI elements chi tiết: ComboBox, CheckBox, Button, TextBox             ║
║  • API specifications: paths, HTTP methods, request/response             ║
║  • Step-by-step logic: 1.1, 1.2, 1.3... hoặc numbered sequences         ║
║  • Database field changes: specific column names, data types             ║
║  • Event handlers: onClick, onChange, onLoad với logic cụ thể           ║
║  • Session/Storage keys: sessionStorage, localStorage specifics         ║
║  • Validation rules chi tiết: field-level validations                   ║
║                                                                           ║
║  BD (Basic Design) - Khi có:                                              ║
║  ─────────────────────────────                                            ║
║  • High-level flow diagrams (không có implementation details)            ║
║  • Architecture overview                                                  ║
║  • Screen transitions (không có UI element details)                      ║
║  • System boundaries và interfaces (high-level)                          ║
║  • Use case descriptions (không có step-by-step logic)                   ║
║                                                                           ║
║  DB (Database Design) - Khi có:                                           ║
║  ─────────────────────────────                                            ║
║  • Table definitions với columns                                          ║
║  • Data types, constraints, indexes                                       ║
║  • Entity-Relationship descriptions                                       ║
║  • Primary keys, foreign keys                                             ║
║  • テーブル定義, カラム, インデックス                                     ║
║                                                                           ║
║  API (API Interface Design) - Khi có:                                     ║
║  ─────────────────────────────────────                                    ║
║  • API endpoint definitions                                               ║
║  • Request/Response schemas                                               ║
║  • Authentication/Authorization specs                                     ║
║  • Error codes và responses                                               ║
║  • インターフェース設計, APIパス, メソッド                                ║
║                                                                           ║
║  BR (Business Requirements) - Khi có:                                     ║
║  ─────────────────────────────────────                                    ║
║  • Business rules và policies                                             ║
║  • Functional requirements (what, not how)                                ║
║  • User stories / Use cases (high-level)                                  ║
║  • Acceptance criteria                                                    ║
║  • 要件定義, 機能要件, ビジネスルール                                     ║
║                                                                           ║
║  UI-HINT (UI Hints/Guidelines) - Khi có:                                  ║
║  ─────────────────────────────────────────                                ║
║  • UI layout guidelines                                                   ║
║  • Color schemes, fonts                                                   ║
║  • UX patterns                                                            ║
║  • Responsive design rules                                                ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Japanese Document Patterns

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  JAPANESE CONTENT INDICATORS                                              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  Detail Design (DD) indicators:                                           ║
║  • 画面項目一覧 với chi tiết UI controls                                  ║
║  • イベント với processing logic                                          ║
║  • 処理フロー với numbered steps                                          ║
║  • API paths: {URL}/api/...                                               ║
║  • HTTP methods: POST, GET, PUT, DELETE                                   ║
║  • パラメータ với specific names                                          ║
║                                                                           ║
║  Basic Design (BD) indicators:                                            ║
║  • 機能概要 (high-level only)                                             ║
║  • 画面遷移図 (screen flow, not details)                                  ║
║  • システム構成 (architecture)                                            ║
║  • 処理概要 (không có step-by-step)                                       ║
║                                                                           ║
║  Database (DB) indicators:                                                ║
║  • テーブル定義                                                           ║
║  • カラム一覧                                                             ║
║  • インデックス定義                                                       ║
║  • 主キー, 外部キー                                                       ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

---

## 📊 CONFIDENCE LEVELS (V3.0 Updated)

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  CONFIDENCE LEVELS - CONTENT-FIRST                                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  HIGH Confidence:                                                         ║
║  ────────────────                                                         ║
║  • Content đọc được và phân tích rõ ràng                                 ║
║  • Multiple indicators match cùng 1 classification                       ║
║  • Content và filename confirm nhau                                       ║
║  → Có thể tin tưởng kết quả                                              ║
║                                                                           ║
║  MEDIUM Confidence:                                                       ║
║  ──────────────────                                                       ║
║  • Content đọc được nhưng indicators không rõ ràng                       ║
║  • Hoặc: Content và filename conflict (tin content hơn)                  ║
║  → Recommend human verification                                          ║
║                                                                           ║
║  LOW Confidence:                                                          ║
║  ───────────────                                                          ║
║  • Không đọc được content (Excel binary limitation)                      ║
║  • Chỉ dựa vào filename pattern                                          ║
║  → REQUIRE human verification                                            ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Confidence Output Format

```yaml
classification:
  file: "外部設計書_ユーザー管理画面.xlsx"
  type: DD
  confidence: HIGH
  method: content-based
  evidence:
    - "画面項目一覧 sheet với UI controls chi tiết (ComboBox, CheckBox)"
    - "イベント sheet với API paths và HTTP methods"
    - "Step-by-step processing logic (1.1, 1.2, 1.3...)"
  note: "Filename suggests BD (外部設計書) but content is clearly DD"
```

---

## 🔧 NAME-BASED FALLBACK RULES

Chỉ sử dụng khi KHÔNG đọc được content:

```yaml
# Name-based rules (FALLBACK only)
# Confidence: LOW - cần human verification

name_patterns:
  # Japanese standard naming
  外部設計書: BD    # Basic/External Design
  基本設計書: BD    # Basic Design  
  詳細設計書: DD    # Detail Design
  画面設計書: DD    # Screen Design (Detail level)
  DBテーブル設計書: DB
  テーブル定義書: DB
  APIインターフェース: API
  API設計書: API
  要件定義書: BR
  機能要件書: BR
  
  # English patterns
  basic_design: BD
  detail_design: DD
  database_design: DB
  api_spec: API
  requirements: BR
```

⚠️ **WARNING:** Name-based classification có thể SAI vì mỗi project có naming convention khác nhau. Luôn flag là LOW confidence.

---

## Usage

```bash
# Classify commands
/f5-classify <path>                    # Classify files (default: Japanese + Vietnamese)
/f5-classify <path> --dry-run          # Preview without creating files
/f5-classify <path> --lang=en          # Add English version
/f5-classify <path> --lang=all         # All languages (ja + vi + en)
/f5-classify <file.xlsx>               # Classify single file

# Review & Approval commands
/f5-classify review                    # Xem Coverage Summary
/f5-classify approve                   # Xác nhận classify result
/f5-classify approve --acknowledge-missing   # Approve và acknowledge missing sections
```

## Arguments

- `path`: Đường dẫn tới file hoặc thư mục cần classify
- `--dry-run`: Preview classification mà không tạo files
- `--lang`: Chọn ngôn ngữ output
  - Default (không flag): Japanese (gốc) + Vietnamese
  - `--lang=en`: Thêm English
  - `--lang=all`: Tất cả (Japanese + Vietnamese + English)

---

## 🆕 WORKFLOW (V3.0)

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         CLASSIFY WORKFLOW V3.0                                   │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  Step 1              Step 2              Step 3              Step 4              │
│  ──────              ──────              ──────              ──────              │
│  /f5-classify   →   /f5-classify   →   /f5-classify   →   /f5-gate             │
│  <path>              review              approve             check D1            │
│                                                                                  │
│  Content-first       Xem Coverage        Human confirm       Check D1 Gate       │
│  Classification      + Confidence        classify result                         │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### ⚠️ RULE QUAN TRỌNG

```
1. LUÔN đọc content trước khi classify (nếu có thể)
2. Nếu không đọc được content → flag LOW confidence
3. Nếu content và filename conflict → TIN CONTENT
4. Human verification REQUIRED cho LOW confidence items
```

---

## 📋 OUTPUT FORMAT (V3.0)

### Main Output

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  📋 KẾT QUẢ PHÂN LOẠI INPUT (V3.0 - CONTENT-FIRST)                        ║
╠═══════════════════════════════════════════════════════════════════════════╣

Nguồn: .f5/input/raw/excel/
Files đã phân tích: 5
Method: CONTENT-BASED ✅

⭐ CLASSIFICATION QUALITY: HIGH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✓ Content analysis: 5/5 files (100%)
✓ Classification confidence: HIGH (4), MEDIUM (1)
✓ Coverage: 5/10 expected sections (50%)

CLASSIFIED FILES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| # | File | Type | Confidence | Method | Evidence |
|---|------|------|------------|--------|----------|
| 1 | 外部設計書_ユーザー管理画面.xlsx | DD | HIGH | content | UI details, API specs |
| 2 | 詳細設計書_共通処理.xlsx | DD | HIGH | content | Processing logic |
| 3 | 詳細設計書_ユーザー管理.xlsx | DD | HIGH | content | Event handlers |
| 4 | DBテーブル設計書_マスタ管理.xlsx | DB | HIGH | content | Table definitions |
| 5 | APIインターフェース設計書_共通API.xlsx | API | HIGH | content | Endpoint specs |

CONTENT ANALYSIS NOTES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📄 外部設計書_ユーザー管理画面.xlsx
   Filename suggests: BD (外部設計書)
   Content analysis: DD (Detail Design)
   → Classified as: DD (based on content)
   Evidence:
   • 画面項目一覧: ComboBox, CheckBox, Button chi tiết
   • イベント: API paths (/api/combo/plan), HTTP methods
   • Step-by-step logic: 1.1, 1.2, 1.3...

╚═══════════════════════════════════════════════════════════════════════════╝
```

### LOW Confidence Warning

```
⚠️ LOW CONFIDENCE ITEMS (Name-based only):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| File | Classified As | Reason |
|------|---------------|--------|
| example.xlsx | DD | Cannot read content - classified by name only |

⚠️ HUMAN VERIFICATION REQUIRED for LOW confidence items
   Options:
   1. Open file manually to verify classification
   2. Convert to CSV for content analysis
   3. Use MCP Excel Server to read content
```

---

## 📊 COVERAGE DECLARATION - FULL TEMPLATE (V3.0)

### File: `.f5/input/classified/_coverage.yaml`

```yaml
# ============================================================================
# COVERAGE DECLARATION - CANONICAL FORMAT
# Generated by f5-classify v3.0 (Content-First)
# ============================================================================

# ============================================================================
# 1. METADATA
# ============================================================================
metadata:
  generated_at: "2025-12-26T12:00:00+07:00"
  generated_by: "f5-classify"
  version: "3.0"
  classification_method: "content-first"
  source_path: ".f5/input/raw/excel/"
  workflow_template: "enterprise-app"

# ============================================================================
# 2. ANALYZED SOURCES (V3.0 - với method và evidence)
# ============================================================================
analyzed_sources:
  - file: "外部設計書_ユーザー管理画面.xlsx"
    type: excel
    classification: DD
    confidence: HIGH
    method: content-based
    evidence:
      - "画面項目一覧 sheet với UI controls (ComboBox, CheckBox)"
      - "イベント sheet với API paths và methods"
      - "Step-by-step processing logic"
    name_suggests: BD
    note: "Content overrides filename suggestion"
    status: complete

  - file: "詳細設計書_共通処理.xlsx"
    type: excel
    classification: DD
    confidence: HIGH
    method: content-based
    evidence:
      - "Processing logic details"
      - "Function specifications"
    status: complete

# ============================================================================
# 3. CLASSIFICATION SUMMARY
# ============================================================================
classification_summary:
  DD: 3
  DB: 1
  API: 1
  BD: 0
  BR: 0
  total: 5

# ============================================================================
# 4. CONFIDENCE LEVELS (V3.0 Updated)
# ============================================================================
confidence:
  overall: HIGH
  by_file:
    - file: "外部設計書_ユーザー管理画面.xlsx"
      level: HIGH
      method: content-based
      reason: "Multiple DD indicators found in content"
    - file: "詳細設計書_共通処理.xlsx"
      level: HIGH
      method: content-based
      reason: "Processing logic clearly indicates DD"
  
  distribution:
    HIGH: 4
    MEDIUM: 1
    LOW: 0
  
  content_based: 5
  name_based: 0

# ============================================================================
# 5. CONTENT ANALYSIS RESULTS (NEW in V3.0)
# ============================================================================
content_analysis:
  files_analyzed: 5
  content_readable: 5
  content_not_readable: 0
  
  indicators_found:
    DD_indicators:
      - "UI controls (ComboBox, CheckBox, Button)"
      - "API paths and HTTP methods"
      - "Step-by-step processing logic"
      - "Event handlers"
    DB_indicators:
      - "Table definitions"
      - "Column specifications"
    API_indicators:
      - "Endpoint definitions"
      - "Request/Response schemas"
  
  conflicts_resolved:
    - file: "外部設計書_ユーザー管理画面.xlsx"
      name_suggests: BD
      content_indicates: DD
      resolution: "Content-based (DD)"
      reason: "Content analysis is more accurate than naming convention"

# ============================================================================
# 6-12. (Giữ nguyên từ V2.1)
# ============================================================================
# ... missing_sections, confirmations_needed, uncertainties,
# ... statistics, extracted_items_summary, technical_limitations,
# ... classification_quality_level, approval
```

---

## 🔗 INTEGRATION VỚI MCP EXCEL

### Để có Content-based classification tốt nhất:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  MCP EXCEL INTEGRATION                                                          │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  Với MCP Excel Server:                                                          │
│  • excel:excel_describe_sheets → Lấy danh sách sheets                           │
│  • excel:excel_read_sheet → Đọc nội dung từng sheet                             │
│  • Phân tích content → Xác định classification                                  │
│  • Confidence: HIGH                                                             │
│                                                                                  │
│  Không có MCP Excel:                                                            │
│  • Convert Excel → CSV trước                                                    │
│  • Hoặc: Fallback to name-based (LOW confidence)                                │
│                                                                                  │
│  Config file: ~/.claude/mcp_servers.json                                        │
│  {                                                                               │
│    "mcpServers": {                                                              │
│      "excel": {                                                                 │
│        "command": "npx",                                                        │
│        "args": ["-y", "@negokaz/excel-mcp-server"]                             │
│      }                                                                          │
│    }                                                                            │
│  }                                                                              │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## EXAMPLES

### Example 1: Content-based Classification (Ideal)

```bash
# MCP Excel available, content readable
/f5-classify .f5/input/raw/excel/

# Output:
# 外部設計書_ユーザー管理画面.xlsx → DD (HIGH confidence, content-based)
# Evidence: UI controls, API specs, step-by-step logic
```

### Example 2: Name-based Fallback (Limited)

```bash
# MCP Excel NOT available, content NOT readable
/f5-classify .f5/input/raw/excel/

# Output:
# 外部設計書_ユーザー管理画面.xlsx → BD (LOW confidence, name-based)
# ⚠️ WARNING: Classification based on filename only - VERIFY MANUALLY
```

### Example 3: Content vs Name Conflict

```bash
# Content analysis shows DD, but filename says 外部設計書 (BD)
/f5-classify .f5/input/raw/excel/外部設計書_ユーザー管理画面.xlsx

# Output:
# Type: DD (not BD)
# Confidence: HIGH
# Note: "Filename suggests BD but content clearly indicates DD"
# → TRUST CONTENT over filename
```

---

## 📝 REVISION HISTORY

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2024-12-23 | Initial version |
| 2.0 | 2024-12-25 | Added Coverage Declaration, Source Mapping, Dual-pass Analysis |
| 2.1 | 2024-12-25 | Added Statistics, Technical Limitations, Quality Level |
| 3.0 | 2024-12-26 | **Content-First Classification** - Phân loại dựa trên nội dung thay vì tên file |

---

## ⚠️ KNOWN LIMITATIONS

| Limitation | Impact | Workaround |
|------------|--------|------------|
| Excel binary without MCP | Cannot read content | Convert to CSV or use MCP Excel |
| PDF tables | Cannot extract reliably | Manual extraction |
| Complex nested structures | May miss some indicators | Human review |
| Mixed-language content | Analysis may be less accurate | Explicit language hints |
