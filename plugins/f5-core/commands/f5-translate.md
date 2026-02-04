---
description: Translate Japanese documents to Vietnamese using AI assistance (Claude Code)
argument-hint: <path> [--dry-run] [--format=xlsx|pdf|md] [--batch] [review|approve]
---

# /f5-translate - Document Translation Command V1.2

Dịch tài liệu tiếng Nhật sang tiếng Việt với sự hỗ trợ của AI, phục vụ BRSE/Comtor.

> **Version:** 1.2
> **Nguyên tắc:** AI hỗ trợ - BRSE/Comtor quyết định
> **Vai trò:** BRSE, Comtor, BA
> **New in V1.2:** AI Flag (Không Quyết) + Uncertainty Tracking

---

## 🎯 VAI TRÒ CỦA F5-TRANSLATE

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  f5-translate KHÔNG PHẢI là dịch máy hoàn toàn                                  │
│  f5-translate KHÔNG thay thế BRSE/Comtor                                        │
├─────────────────────────────────────────────────────────────────────────────────┤
│  VAI TRÒ ĐÚNG:                                                                  │
│  ✅ Hỗ trợ dịch nhanh các đoạn text                                            │
│  ✅ Gợi ý thuật ngữ chuyên ngành                                               │
│  ✅ Giữ nguyên cấu trúc tài liệu                                               │
│  ✅ Highlight phần cần BRSE review kỹ                                          │
│  ✅ Tạo glossary thuật ngữ                                                     │
│  ✅ Track translation coverage                                                  │
│  ✅ 🆕 Flag uncertainty - không tự tin giả                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│  QUYẾT ĐỊNH CUỐI CÙNG: BRSE / Comtor                                           │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## ⚠️ TRANSLATION RULES (MANDATORY - V1.2)

### Nguyên tắc cốt lõi: AI FLAG, KHÔNG QUYẾT

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  AI KHÔNG ĐƯỢC "TỰ TIN GIẢ" VỚI THUẬT NGỮ KỸ THUẬT                              │
│  Nếu không chắc → PHẢI FLAG, không được lặng lẽ chọn 1 cách dịch               │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  3 LOẠI TEXT TRONG TÀI LIỆU JP:                                                 │
│                                                                                  │
│  1️⃣ TECHNICAL IDENTIFIERS → ❌ KHÔNG DỊCH                                      │
│     • API paths: /api/v1/traffic-control                                        │
│     • Field names: traffic_type, start_date                                     │
│     • Codes/Enums: ACTIVE, INACTIVE, ERR_001                                    │
│     • DB columns: T_USER.USER_ID                                                │
│     → Action: Keep as-is, ghi rõ lý do                                          │
│                                                                                  │
│  2️⃣ DOMAIN TERMS (EN common) → ⚠️ FLAG CHO BRSE                                │
│     • mode, status, flag, master, batch                                         │
│     • システム管理モード, 処理区分, 運用種別                                         │
│     → Action: Gợi ý options, BRSE quyết định                                    │
│                                                                                  │
│  3️⃣ BUSINESS CONTENT → ✅ DỊCH + FLAG NẾU AMBIGUOUS                            │
│     • Rules, flows, explanations                                                │
│     • 処理 có thể là "xử lý" hoặc "màn hình xử lý"                              │
│     → Action: Dịch theo context, flag nếu nhiều nghĩa                           │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### AI MUST (Bắt buộc)

```yaml
translation_rules:
  must:
    - Keep technical identifiers UNCHANGED
    - Use glossary if term exists and verified
    - Flag ANY term with multiple plausible meanings
    - NEVER silently choose between domain translations
    - Provide context where term was found
    - List all possible translations for uncertain terms
    
  must_not:
    - Auto-translate technical codes
    - Assume single meaning for ambiguous terms
    - Hide uncertainty behind confident translation
    - Skip flagging just because "it seems obvious"
```

### Uncertainty Output Format

```
⚠️ UNCERTAIN TERM DETECTED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Japanese: システム管理モード

Contexts found:
  1. 「システム管理モードに切り替える」 (Sheet: 画面項目, Cell: B15)
  2. 「システム管理モード中は編集不可」 (Sheet: 処理フロー, Cell: C8)

Possible translations:
  1. Chế độ điều khiển giao thông
  2. Chế độ quản lý giao thông  
  3. Chế độ kiểm soát giao thông
  4. [Keep as-is: システム管理モード]

Recommendation:
  → Verify with customer or domain expert
  → Check existing glossary in similar projects

Status: PENDING_BRSE_REVIEW
```

### Keep-As-Is Output Format

```
✅ TECHNICAL IDENTIFIER (Kept as-is)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Original: traffic_type
Translation: traffic_type
Reason: Database column name - technical identifier
Location: Sheet: テーブル定義, Column: フィールド名
```

---

## 📊 WORKFLOW VỊ TRÍ TRONG PIPELINE

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         DOCUMENT PIPELINE                                        │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  PHASE 0.5: TRANSLATE (NEW)              PHASE 1: CLASSIFY                      │
│  ─────────────────────────               ────────────────────                    │
│                                                                                  │
│  Customer        /f5-translate     BRSE        /f5-classify                     │
│  Docs (JP)   →   <path>        →   Review  →   <path>                           │
│                                    Approve                                       │
│                                                                                  │
│  Output:                           Output:                                       │
│  • translated/                     • classified/                                │
│  • _translation-report.md          • _coverage.yaml                             │
│  • _glossary.yaml                  • _classification-report.md                  │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🧠 TRANSLATION STRATEGY

### AI-Assisted Translation Process

```
╔═══════════════════════════════════════════════════════════════════════════╗
║  TRANSLATION LEVELS                                                       ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  LEVEL 1: AUTO (AI dịch tự động)                                          ║
║  ─────────────────────────────────                                        ║
║  • Simple text, UI labels                                                 ║
║  • Standard technical terms                                               ║
║  • Numbers, dates, codes                                                  ║
║  → Confidence: HIGH                                                       ║
║                                                                           ║
║  LEVEL 2: SUGGEST (AI gợi ý, BRSE chọn)                                   ║
║  ─────────────────────────────────────────                                ║
║  • Business terminology                                                   ║
║  • Domain-specific terms                                                  ║
║  • Ambiguous phrases                                                      ║
║  → Confidence: MEDIUM, cần review                                         ║
║                                                                           ║
║  LEVEL 3: MANUAL (BRSE dịch thủ công)                                     ║
║  ──────────────────────────────────────                                   ║
║  • Complex business rules                                                 ║
║  • Legal/contractual terms                                                ║
║  • Customer-specific terminology                                          ║
║  → Confidence: LOW, flag cho human                                        ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Domain-Specific Glossary

```yaml
# .f5/config/glossary.yaml
glossary:
  # Domain-specific Terms
  システム管理: "Quản lý hệ thống"
  ユーザー管理: "Quản lý người dùng"
  CSV取込: "Import CSV"
  データ取得処理: "Tự động lấy dữ liệu"
  
  # Technical Terms
  画面設計書: "Tài liệu thiết kế màn hình"
  外部設計書: "Tài liệu thiết kế ngoài (Basic Design)"
  詳細設計書: "Tài liệu thiết kế chi tiết (Detail Design)"
  テーブル定義: "Định nghĩa bảng"
  
  # UI Elements
  コンボボックス: "ComboBox"
  チェックボックス: "CheckBox"
  テキストボックス: "TextBox"
  ボタン: "Button"
```

---

## 🔄 DUAL-PASS CONTENT COMPLETENESS CHECK (NEW V1.1)

### Tại sao cần Dual-Pass cho Translation?

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  TRANSLATION vs CLASSIFY - KHÁC MỤC TIÊU                                        │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  TRANSLATE expected-content:           CLASSIFY expected-sections:              │
│  ─────────────────────────────         ───────────────────────────              │
│  "Bản dịch giữ lại ĐỦ không?"          "Tài liệu có ĐỦ loại không?"             │
│                                                                                  │
│  Mục tiêu: COMPLETENESS & FIDELITY     Mục tiêu: FUNCTIONAL COVERAGE            │
│  (Không mất thông tin khi dịch)        (Đủ để làm spec)                          │
│                                                                                  │
│  Ví dụ check:                          Ví dụ check:                             │
│  • UI labels đã dịch?                  • Có NFR không?                          │
│  • Error messages đã dịch?             • Có Error Handling không?               │
│  • Validation rules đã dịch?           • Có Screen Flow không?                  │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Dual-Pass Flow

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  DUAL-PASS TRANSLATION CHECK                                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  PASS 1: Content Detection (Phát hiện loại nội dung trong JP)                   │
│  ─────────────────────────────────────────────────────────────                  │
│  AI + BRSE scan file JP và xác định:                                            │
│  ✅ Có UI labels                                                                │
│  ✅ Có validation rules                                                         │
│  ✅ Có error messages                                                           │
│  ✅ Có notes/備考                                                               │
│  ❌ Không có API descriptions                                                   │
│                                                                                  │
│  PASS 2: Completeness Check (So sánh JP vs VI)                                  │
│  ─────────────────────────────────────────────                                  │
│  | Content Type      | Found JP | Translated VI | Status          |            │
│  |-------------------|----------|---------------|-----------------|            │
│  | UI Labels         | ✅       | ✅            | ✅ OK           |            │
│  | Validation Rules  | ✅       | ❌            | ⚠️ MISSING      |            │
│  | Error Messages    | ✅       | ❌            | ⚠️ MISSING      |            │
│  | Notes/備考        | ✅       | ✅            | ✅ OK           |            │
│  | API Descriptions  | ❌       | -             | ➖ N/A          |            │
│                                                                                  │
│  OUTPUT:                                                                         │
│  • translation_completeness: 50% (2/4 content types)                            │
│  • missing_content_types: [validation_rules, error_messages]                    │
│  • recommendation: REVIEW_MISSING_CONTENT                                       │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Expected Content Types

```yaml
# File: translation-expected-content.yaml

expected_content_types:
  # CRITICAL - Bắt buộc dịch nếu có
  - id: ui_labels
    name: "UI Labels"
    required: true
    priority: critical
    
  - id: validation_rules
    name: "Validation Rules"
    required: true
    priority: critical
    
  - id: error_messages
    name: "Error Messages"
    required: true
    priority: critical
    
  - id: field_descriptions
    name: "Field Descriptions"
    required: true
    priority: critical

  # HIGH - Nên dịch
  - id: processing_logic
    name: "Processing Logic"
    required: true
    priority: high
    
  - id: business_rules_text
    name: "Business Rules (Text)"
    required: true
    priority: high

  # MEDIUM - Khuyến khích dịch
  - id: notes_remarks
    name: "Notes / Remarks"
    required: false
    priority: medium
    
  # PRESERVE - Giữ nguyên KHÔNG dịch
  - id: technical_identifiers
    name: "Technical Identifiers"
    action: preserve  # API paths, field names, codes
```

### Approval Rules (Based on Completeness)

| Completeness | Critical Missing | Action |
|--------------|------------------|--------|
| >= 90% | 0 | ✅ Auto Approve |
| >= 70% | 0 | ⚠️ Review then Approve |
| >= 70% | > 0 | ⚠️ Must Review Critical |
| < 70% | any | ⛔ Block - Incomplete |
| < 50% | any | ⛔ Block - Seriously Incomplete |

---

## 📁 FOLDER STRUCTURE

```
.f5/input/
├── raw/                               # Files gốc từ customer (JP)
│   └── excel/
│       └── 0203/
│           ├── 外部設計書_サンプル画面.xlsx
│           └── 詳細設計書_xxx.xlsx
│
├── translated/                        # 🆕 Files đã dịch (VI)
│   ├── _translation-report.md         # Report tổng hợp
│   ├── _glossary.yaml                 # Thuật ngữ đã dùng
│   ├── _pending-review.yaml           # Items cần BRSE review
│   │
│   └── excel/
│       └── 0203/
│           ├── 外部設計書_サンプル画面.vi.xlsx    # Bản dịch
│           ├── 外部設計書_サンプル画面.vi.md      # Export markdown
│           └── _translation-notes.md               # Ghi chú dịch
│
└── classified/                        # Output của f5-classify
    └── ...
```

---

## 🔧 COMMANDS

### Basic Usage

```bash
# Translate single file
/f5-translate .f5/input/raw/excel/0203/外部設計書_サンプル画面.xlsx

# Translate entire folder
/f5-translate .f5/input/raw/excel/0203/

# Dry run - preview without creating files
/f5-translate .f5/input/raw/excel/0203/ --dry-run

# Batch mode - translate all files in raw/
/f5-translate --batch
```

### Review & Approval

```bash
# Review translation summary
/f5-translate review

# Review specific file
/f5-translate review 外部設計書_サンプル画面.xlsx

# Approve translation (by BRSE)
/f5-translate approve

# Approve with notes
/f5-translate approve --note "Đã review thuật ngữ với KH"
```

### Glossary Management

```bash
# View current glossary
/f5-translate glossary

# Add new term
/f5-translate glossary add "システム管理" "Quản lý giao thông"

# Import glossary from file
/f5-translate glossary import ./custom-glossary.yaml
```

---

## 📋 TRANSLATION WORKFLOW FOR BRSE/COMTOR

### Step-by-Step Guide

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  BRSE/COMTOR WORKFLOW                                                           │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  STEP 1: Chuẩn bị                                                               │
│  ────────────────────                                                           │
│  1.1. Nhận files từ customer                                                    │
│  1.2. Copy vào .f5/input/raw/excel/{module}/                                    │
│  1.3. Check glossary có đủ thuật ngữ không                                      │
│       /f5-translate glossary                                                    │
│                                                                                  │
│  STEP 2: Dịch với AI                                                            │
│  ───────────────────                                                            │
│  2.1. Chạy translate command                                                    │
│       /f5-translate .f5/input/raw/excel/0203/                                   │
│                                                                                  │
│  2.2. AI sẽ:                                                                    │
│       • Đọc nội dung file (MCP Excel hoặc export CSV)                          │
│       • Dịch theo glossary                                                      │
│       • Flag các phần cần review                                                │
│       • Tạo translation report                                                  │
│                                                                                  │
│  STEP 3: Review                                                                 │
│  ────────────────                                                               │
│  3.1. Xem report                                                                │
│       /f5-translate review                                                      │
│                                                                                  │
│  3.2. Check các items flagged:                                                  │
│       • MEDIUM confidence → verify thuật ngữ                                    │
│       • LOW confidence → dịch thủ công                                          │
│                                                                                  │
│  3.3. Edit trực tiếp trong file .vi.xlsx hoặc .vi.md                           │
│                                                                                  │
│  STEP 4: Approve                                                                │
│  ───────────────                                                                │
│  4.1. Approve khi đã review xong                                                │
│       /f5-translate approve                                                     │
│                                                                                  │
│  4.2. Translation sẽ được lock, ready cho classify                             │
│                                                                                  │
│  STEP 5: Hand-off to Classify                                                   │
│  ────────────────────────────                                                   │
│  5.1. Chạy classify trên bản dịch                                              │
│       /f5-classify .f5/input/translated/excel/0203/                            │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📊 OUTPUT FORMAT

### Translation Report

```markdown
# 📋 BÁO CÁO DỊCH TÀI LIỆU

## Thông tin chung

| Thông tin | Giá trị |
|-----------|---------|
| Nguồn | .f5/input/raw/excel/0203/ |
| Files | 5 |
| Ngày dịch | 2025-12-26 |
| Người thực hiện | BRSE + Claude |

## Thống kê dịch

| Level | Count | % |
|-------|-------|---|
| AUTO (HIGH confidence) | 450 | 75% |
| SUGGEST (MEDIUM confidence) | 120 | 20% |
| MANUAL (LOW confidence) | 30 | 5% |

## Files đã dịch

| # | File gốc | File dịch | Coverage | Status |
|---|----------|-----------|----------|--------|
| 1 | 外部設計書_サンプル画面.xlsx | *.vi.xlsx | 95% | ✅ Complete |
| 2 | 詳細設計書_共通処理.xlsx | *.vi.xlsx | 88% | ⚠️ Pending review |

## Items cần review

### 🟡 MEDIUM Confidence (cần verify)

| File | Sheet | Cell | Japanese | Vietnamese (gợi ý) |
|------|-------|------|----------|-------------------|
| 外部設計書_xxx.xlsx | 画面項目 | B5 | 交通規制情報 | Thông tin quy định giao thông |

### 🔴 LOW Confidence (cần dịch thủ công)

| File | Sheet | Cell | Japanese | Lý do |
|------|-------|------|----------|-------|
| 詳細設計書_xxx.xlsx | 処理フロー | C12 | 物件区分による処理分岐... | Business logic phức tạp |

## Thuật ngữ mới

| Japanese | Vietnamese | Source | Added by |
|----------|------------|--------|----------|
| システム管理モード | Chế độ quản lý giao thông | 外部設計書_xxx.xlsx | AI suggest |
```

### Translation Coverage YAML

```yaml
# .f5/input/translated/_translation-coverage.yaml
metadata:
  generated_at: "2025-12-26T10:00:00+07:00"
  source_path: ".f5/input/raw/excel/0203/"
  translator: "brse + claude"
  version: "1.1"  # With completeness check

files:
  - source: "外部設計書_サンプル画面.xlsx"
    target: "外部設計書_サンプル画面.vi.xlsx"
    coverage: 95%
    confidence:
      high: 75%
      medium: 20%
      low: 5%
    pending_review: 12
    status: pending_approval

  - source: "詳細設計書_共通処理.xlsx"
    target: "詳細設計書_共通処理.vi.xlsx"
    coverage: 88%
    status: in_progress

glossary_additions:
  - term: "システム管理モード"
    translation: "Chế độ quản lý giao thông"
    confidence: medium
    verified: false

# ═══════════════════════════════════════════════════════════════
# 🆕 TRANSLATION COMPLETENESS (V1.1 - Dual-Pass)
# ═══════════════════════════════════════════════════════════════
translation_completeness:
  expected_types: 6          # Số loại content tìm thấy trong JP
  translated_types: 4        # Số loại đã dịch
  coverage_percent: 67%      # translated / expected * 100
  
  content_type_status:
    - type: ui_labels
      priority: critical
      found_in_jp: true
      translated: true
      status: "✅ OK"
      
    - type: field_descriptions
      priority: critical
      found_in_jp: true
      translated: true
      status: "✅ OK"
      
    - type: validation_rules
      priority: critical
      found_in_jp: true
      translated: false
      status: "⚠️ MISSING"
      location: "Sheet: 入力チェック"
      
    - type: error_messages
      priority: critical
      found_in_jp: true
      translated: false
      status: "⚠️ MISSING"
      location: "Sheet: エラーメッセージ"
      
    - type: processing_logic
      priority: high
      found_in_jp: true
      translated: true
      status: "✅ OK"
      
    - type: notes_remarks
      priority: medium
      found_in_jp: true
      translated: true
      status: "✅ OK"

  missing_content_types:
    - validation_rules
    - error_messages
    
  critical_missing: 2        # Số critical items bị thiếu
  
  recommendation: "REVIEW_MISSING_CONTENT"
  can_approve: false         # Block vì có critical missing

# ═══════════════════════════════════════════════════════════════
# 🆕 UNCERTAINTY TRACKING (V1.2 - AI Flag, Human Decide)
# ═══════════════════════════════════════════════════════════════
uncertain_terms:
  count: 3
  items:
    - term: "システム管理モード"
      contexts:
        - text: "システム管理モードに切り替える"
          location: "Sheet: 画面項目, Cell: B15"
        - text: "システム管理モード中は編集不可"
          location: "Sheet: 処理フロー, Cell: C8"
      suggestions:
        - "Chế độ điều khiển giao thông"
        - "Chế độ quản lý giao thông"
        - "Chế độ kiểm soát giao thông"
      status: pending_brse_review
      
    - term: "処理区分"
      contexts:
        - text: "処理区分を選択"
          location: "Sheet: 画面項目, Cell: D20"
      suggestions:
        - "Loại xử lý"
        - "Phân loại xử lý"
        - "Phân loại process"
      status: pending_brse_review
      
    - term: "運用種別"
      contexts:
        - text: "運用種別マスタ"
          location: "Sheet: テーブル定義, Cell: A5"
      suggestions:
        - "Loại vận hành"
        - "Phân loại operation"
      status: pending_brse_review

  # ⚠️ WARNING nếu không có uncertain terms
  warning: |
    Tài liệu JP luôn có ambiguity.
    Nếu uncertain_terms.count = 0 → AI có thể đang "tự tin giả"
    BRSE nên double-check.

# ═══════════════════════════════════════════════════════════════
# 🆕 KEPT-AS-IS LIST (Technical Identifiers)
# ═══════════════════════════════════════════════════════════════
kept_as_is:
  count: 15
  items:
    - original: "traffic_type"
      reason: "Database column name"
      location: "Sheet: テーブル定義"
      
    - original: "/api/v1/traffic-control"
      reason: "API endpoint path"
      location: "Sheet: API設計"
      
    - original: "ACTIVE"
      reason: "Enum value / code"
      location: "Sheet: コード定義"
      
    - original: "BTN_SEARCH"
      reason: "UI element ID"
      location: "Sheet: 画面項目"
      
    - original: "ERR_001"
      reason: "Error code"
      location: "Sheet: エラーメッセージ"

approval:
  approved: false
  approved_by: null
  approved_at: null
  completeness_acknowledged: false
  uncertainty_reviewed: false  # 🆕 BRSE đã review uncertain terms
```

---

## 🔗 INTEGRATION VỚI CLASSIFY

### Pre-condition cho f5-classify

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  CLASSIFY PRE-CONDITIONS (Updated)                                              │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  ⚠️ f5-classify sẽ CHECK:                                                       │
│                                                                                  │
│  1. Có translation result không?                                                │
│     • Nếu KHÔNG → OK, classify files gốc (JP)                                  │
│     • Nếu CÓ nhưng chưa approved → ⛔ BLOCK                                    │
│     • Nếu CÓ và đã approved → ✅ Classify bản dịch (VI)                        │
│                                                                                  │
│  2. Source path recommendation:                                                 │
│     • Nếu có translated/ → suggest dùng translated/                            │
│     • Nếu không có → dùng raw/                                                  │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## ✅ BRSE REVIEW CHECKLIST (V1.2)

### Quick Review Flow (80% giá trị, 20% effort)

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│  BRSE KHÔNG CẦN ĐỌC TOÀN BỘ FILE                                                │
│  Chỉ cần check 4 sections:                                                       │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  1️⃣ /f5-translate uncertainty                                                  │
│     → Review uncertain terms, quyết định cách dịch                              │
│     → Update glossary                                                            │
│                                                                                  │
│  2️⃣ /f5-translate kept-as-is                                                   │
│     → Verify technical identifiers giữ đúng                                     │
│     → Check không dịch nhầm code                                                │
│                                                                                  │
│  3️⃣ /f5-translate completeness                                                 │
│     → Check missing content types                                               │
│     → Decide: dịch thêm hay acknowledge                                         │
│                                                                                  │
│  4️⃣ LOW confidence items                                                       │
│     → Manual translate nếu cần                                                  │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Approval Checklist

| ✅ | Item | Command |
|----|------|---------|
| ☐ | Uncertain terms đã review | `/f5-translate uncertainty` |
| ☐ | Đã chọn translation cho mỗi uncertain term | Update glossary |
| ☐ | Technical identifiers đúng (không dịch code) | `/f5-translate kept-as-is` |
| ☐ | Content completeness >= 70% | `/f5-translate completeness` |
| ☐ | Critical missing = 0 hoặc acknowledged | `--acknowledge-missing` |
| ☐ | Glossary updated với terms mới | `/f5-translate glossary` |

### Glossary Learning Loop

```
AI flag uncertain term
       ↓
BRSE quyết định
       ↓
Update glossary (với verified: true)
       ↓
Next translate: AI dùng glossary
       ↓
Accuracy tăng dần
```

---

## ⚠️ KNOWN LIMITATIONS

| Limitation | Impact | Workaround |
|------------|--------|------------|
| Excel binary | Không đọc được cell content | MCP Excel hoặc export CSV |
| Complex tables | AI có thể merge sai | BRSE review kỹ bảng phức tạp |
| Domain terms | AI không biết thuật ngữ đặc thù | Maintain glossary |
| Image text | Không dịch được text trong hình | Manual extraction |

---

## 📝 BEST PRACTICES

### Cho BRSE/Comtor

1. **Maintain Glossary**: Update glossary sau mỗi project
2. **Review MEDIUM items**: Đừng skip, có thể sai thuật ngữ
3. **Document decisions**: Ghi chú lý do chọn cách dịch
4. **Verify with customer**: Confirm thuật ngữ đặc thù với KH

### Cho AI Usage

1. **Chunk processing**: Dịch từng sheet, không dịch cả file 1 lần
2. **Preserve structure**: Giữ nguyên row/column structure
3. **Flag uncertainty**: Luôn flag khi không chắc chắn
4. **Use glossary first**: Check glossary trước khi dịch

---

## 📝 REVISION HISTORY

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-26 | Initial version |
| 1.1 | 2025-12-26 | Added Dual-Pass Content Completeness Check |
| 1.2 | 2025-12-26 | Added AI Flag (Không Quyết) + Uncertainty Tracking |

---

## 🔧 COMMANDS REFERENCE

### Basic Commands

```bash
# Translate files
/f5-translate <path>                    # Translate files (default: JP → VI)
/f5-translate <path> --dry-run          # Preview without creating files
/f5-translate --batch                   # Translate all in raw/
```

### Review & Approval

```bash
# Review
/f5-translate review                    # Xem Translation Summary
/f5-translate review <file>             # Review specific file
/f5-translate completeness              # Xem Content Completeness Check
/f5-translate uncertainty               # 🆕 Xem Uncertain Terms list
/f5-translate kept-as-is                # 🆕 Xem Technical Identifiers kept

# Approve
/f5-translate approve                   # Approve (chỉ khi đủ điều kiện)
/f5-translate approve --acknowledge-missing       # Approve với acknowledge thiếu sót
/f5-translate approve --uncertainty-reviewed      # 🆕 Confirm đã review uncertain terms
```

### Glossary Management

```bash
/f5-translate glossary                  # View glossary
/f5-translate glossary add "<JP>" "<VI>"  # Add term
/f5-translate glossary import <file>    # Import from file
```

---

*F5 Framework - AI Support, Human Decide*
