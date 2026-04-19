---
name: Text to Markdown Conversion
description: Convert structured plain-text data (delimiter-separated status trackers, lists, tables) into well-formatted Markdown with emoji status indicators, proper tables, and file renaming.
category: Data Formatting & Presentation
---

# Text to Markdown Conversion Skill

> **Skill ID:** `text_to_markdown`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Text to Markdown Conversion Skill

## 1. Scope Statement

This skill establishes the industrial protocol for transforming structured plain-text data (delimiter-separated trackers, lists, status logs) into high-fidelity Markdown. It enforces strict parsing of consistent delimiters, mapping of textual markers to standardized emoji status indicators (✅/❌/🔄), and the generation of properly aligned Markdown tables. The protocol mandates in-place content replacement and extension renaming (`.txt` → `.md`) while prohibiting the injection of non-original content.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into five operational phases.

### 3.1 Phase 1: Structural Audit & Parsing

1.  **Pattern Detection**: Analyze the source for header lines, data rows, and consistent delimiters (` - `, ` | `, `, `).
2.  **Header Extraction**: Identify column headers from the most complete data row (cross-referencing all rows for maximum coverage).
3.  **Status Mapping**: Map textual markers to standardized emojis:
    - Named text → ✅ (Completed)
    - `X` or Empty → ❌ (Not Done)
    - `(text)` → (✅) (In Progress/Partial)
    - `N/A` → ➖ (Not Applicable)

### 3.2 Phase 2: Markdown Generation

1.  **Table Construction**: Build the Markdown table with Title Case headers and center-aligned status columns (`:---:`).
2.  **Header Conversion**: Transform original metadata/titles into Markdown H1s (`#`) or bolded (`**`) fields.
3.  **Zero Injected Content**: Ensure no summaries, legends, or notes are added unless explicitly requested.

### 3.3 Phase 3: In-Place Transformation

1.  **Content Replacement**: Overwrite the original `.txt` file content with the generated Markdown.
2.  **Rename Execution**: Perform a `git mv` (preferred) or `Rename-Item` to change the extension from `.txt` to `.md`.

### 3.4 Phase 4: Fidelity Verification

1.  **Integrity Check**: Confirm every data row from the source is present in the output table in its original order.
2.  **Status Audit**: Verify that the emoji mapping correctly represents the source text status.
3.  **Link/Path Hygiene**: Ensure any paths mentioned in the text are preserved correctly.

### 3.5 Phase 5: Reporting

1.  **Transformation Summary**: Report the number of rows converted and the new filename.
2.  **Preview Confirmation**: Inform the user that the file is now ready for Markdown preview rendering.

***

## 4. Deep Command Explanation

### 4.1 `git mv <file>.txt <file>.md`
- Renames the file while preserving its history in the Git repository.
- This is the preferred method for extension conversion to avoid "deleting" the old file and "creating" a new one in the Git index.

### 4.2 `:---:` (Table Alignment)
- Markdown table syntax for center-aligning a column.
- Used in the separator row (line 2 of the table) to ensure status emojis are visually centered, improving readability in scannable trackers.

### 4.3 `Get-Content -Raw`
- Reads the entire content of a file as a single string (PowerShell).
- Essential for parsing multi-line header sections and metadata that precede the tabular data.

***

## 5. Prohibited Behaviors

- **No content injection**: Do NOT add legends, progress summaries, or "Converted by Agent" notes.
- **No row reordering**: Maintain the original sequence of data rows exactly as found in the source.
- **No data loss**: Every field and every row must be accounted for in the output table.
- **No separate file creation**: Always perform an in-place replacement followed by a rename.
- **No naming changes**: Preserve the base filename exactly; only the extension changes.

***

## 6. Related Conversations & Traceability

- **Markdown Generation Rules**: [markdown_generation/SKILL.md](../markdown_generation/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
- **Project Structure**: [../project_structure/SKILL.md](../project_structure/SKILL.md)
