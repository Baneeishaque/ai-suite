---
name: Gitignore Rules
description: Audit .gitignore files for common pitfalls — especially directory-ignore patterns that silently break negation rules — and apply verified fixes.
category: Git & Version Control
---

# Gitignore Rules Skill

> **Skill ID:** `gitignore_rules`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Gitignore Rules Skill

## 1. Scope Statement

This skill establishes the industrial protocol for auditing and fixing `.gitignore` files. It identifies structural errors and silent failures, specifically targeting the "directory-ignore + negation" pitfall where Git stops descending into directories, rendering subsequent negation patterns useless. The protocol includes automated detection logic, structural transformation rules (`dir/` → `dir/*`), and mandatory verification using `git check-ignore` to ensure intended files are correctly tracked.

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

### 3.1 Phase 1: Global Discovery

1.  **Inventory**: Find every `.gitignore` in the repository, excluding standard noise directories (`.git`, `node_modules`, `target`, `dist`).
2.  **Indexing**: List all discovered files with their relative paths.

### 3.2 Phase 2: Structural Audit (Pitfall Detection)

1.  **Directory-Ignore Audit**: Identify `dir/` patterns followed by `!dir/` negations.
2.  **Order Audit**: Detect negations (`!`) appearing before the patterns they negate.
3.  **Nesting Audit**: Verify that intermediate directories are un-ignored for deeply nested negations.
4.  **Hygiene Audit**: Scan for trailing whitespace and redundant patterns.

### 3.3 Phase 3: Risk Reporting

1.  **Categorized Table**: Present all structural errors in a table: `Line | Pattern | Issue | Affected Negation`.
2.  **Severity Warning**: Explicitly state that these are **silent failures** with zero warning from Git.

### 3.4 Phase 4: Structural Transformation

1.  **Fixed Patterns**: Convert `dir/` to `dir/*` ONLY when negations are present.
2.  **Reordering**: Move negations after their corresponding ignore patterns.
3.  **Preservation**: Maintain all comments and original user-defined groupings.

### 3.5 Phase 5: Verification (Mandatory)

1.  **Fidelity Check**: Run `git check-ignore -v <path>` for both intended-to-track and intended-to-ignore files.
2.  **Visibility Check**: Run `git ls-files --others --exclude-standard <dir>` to confirm files are visible to Git.

### 3.6 Phase 6: Maintenance & Untracking

1.  **Tracked Audit**: Detect files that are currently tracked but match the new `.gitignore` rules.
2.  **Resolution**: Advise the user on `git rm --cached <file>` for previously committed files.

***

## 4. Deep Command Explanation

### 4.1 `git check-ignore -v <path>`
- `-v` (or `--verbose`): Outputs the pattern, line number, and filename of the `.gitignore` rule that matched.
- This is the authoritative tool for debugging ignore rules. If it returns nothing, the file is NOT ignored.

### 4.2 `git ls-files --others --exclude-standard`
- `--others`: Shows untracked files.
- `--exclude-standard`: Filters the list using standard ignore rules (`.gitignore`, `.git/info/exclude`, etc.).
- This command proves that Git can "see" a file as a candidate for version control.

### 4.3 `git rm --cached <file>`
- Removes the file from the Git index (tracking) but keeps the physical file in the working tree.
- Essential for "applying" `.gitignore` changes to files that were accidentally committed before the ignore rule existed.

***

## 5. Prohibited Behaviors

- **No conversion without negations**: Do NOT convert `dir/` to `dir/*` if no negations exist for that directory.
- **No silent modifications**: Every fix requires an audit report and mandatory verification.
- **No assumption of untracked status**: Always check `git ls-files` before advising ignore changes.
- **No reordering unrelated rules**: Only reorder when fixing a negation-before-ignore violation.
- **No removal of user rules**: Never delete a pattern; only transform it to a functionally correct equivalent.

***

## 6. Related Conversations & Traceability

- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
