---
name: Deleted Files Audit
description: Systematic audit of deleted files in a Git repository — categorizing deletions, scanning for stale references, checking IDE configs, and reporting a safety verdict.
category: Code Hygiene & Maintenance
---

# Deleted Files Audit Skill

> **Skill ID:** `deleted_files_audit`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Deleted Files Audit Skill

## 1. Scope Statement

This skill establishes the systematic protocol for auditing pending file deletions in a Git repository. It ensures that deletions do not cause broken builds, runtime errors, or IDE launch failures by categorizing files, scanning for stale references in source code and configurations, and verifying `.gitignore` coverage. The goal is to provide a structured safety verdict for every deletion before it is committed to history.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into six operational phases.

### 3.1 Phase 1: Detection

Run `git status` and `git diff --name-only --diff-filter=D` to identify all staged and unstaged deletions.

### 3.2 Phase 2: Categorization

Group deleted files into categories (e.g., Generated output, Input data, Source code, Configuration) to assess inherent risk. Use directory-level grouping for bulk deletions.

### 3.3 Phase 3: Reference Scanning

Search the entire codebase (excluding `.git`, `node_modules`, etc.) for references to deleted directory names and file basenames.

- **Tools**: Use `Select-String` (PowerShell) or `grep -rn` (Bash).
- **Classification**: Distinguish between critical (imports, hardcoded paths) and low-severity (comments, documentation) references.

### 3.4 Phase 4: IDE Configuration Audit

Check IDE-specific files for stale references:
- **Eclipse**: `.launch` files in `.metadata`.
- **VS Code**: `.vscode/launch.json`.
- **IntelliJ**: `.idea/runConfigurations/*.xml`.

### 3.5 Phase 5: Gitignore Coverage Audit

Verify if `.gitignore` prevents future accidental commits of similar files (especially for deleted generated artifacts).

### 3.6 Phase 6: Reporting & Verdict

Produce a structured "Verdict Table" covering:
- Group/File Path
- Category
- Source/IDE Reference status
- `.gitignore` status
- **Verdict**: ✅ Safe, ⚠️ Caution, or ❌ Dangerous.

***

## 4. Deep Command Explanation

### 4.1 `git diff --name-only --diff-filter=D`
- `--name-only`: Suppresses the patch output and only shows the file paths.
- `--diff-filter=D`: Filters the output to include only files that have been **D**eleted.
- This command is critical for generating a clean list of targets for the audit without manual filtering.

### 4.2 `git status -M`
- `-M`: Enables rename detection. If a file is deleted and a similar file is added elsewhere, Git will identify it as a rename. This prevents false positives where a "move" is audited as a "deletion".

### 4.3 `grep -rn "pattern" --exclude-dir={...}`
- `-r`: Recursive search.
- `-n`: Print line numbers.
- `--exclude-dir`: Skips high-noise directories (like `.git` or `target`), significantly improving performance and accuracy.

***

## 5. Prohibited Behaviors

- **No auto-committing**: Never stage or commit deletions without user approval.
- **No skipping IDE checks**: Launch configurations must always be audited for stale references.
- **No assumptions**: A file must never be marked "safe" without a full reference scan.
- **No truncated reports**: Every deleted group must appear in the final verdict table.

***

## 6. Related Conversations & Traceability

- **Git Operation Rules**: [../../../ai-agent-rules/git-operation-rules.md](../../../ai-agent-rules/git-operation-rules.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
