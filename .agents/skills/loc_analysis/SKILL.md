---
name: LOC Analysis
description: Calculate lines of code added, deleted, and modified — comparing two codebases or analyzing git history within a scoped feature.
category: Metrics & Reporting
---

# LOC Analysis Skill

> **Skill ID:** `loc_analysis`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)# LOC Analysis Skill

## 1. Scope Statement

This skill establishes the industrial protocol for calculating Lines of Code (LOC) metrics across project transformations and feature integrations. It provides two operational modes: **Baseline Comparison** (measuring deltas between two codebases) and **Git Commit Analysis** (isolating feature footprints within a single repository's history). The protocol ensures metric fidelity by excluding generated artifacts, correctly handling renames, and partitioning feature-related changes from unrelated maintenance noise.

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

### 3.1 Phase 1: Baseline & Scope Definition

1.  **Inventory**: Enumerate all VCS-tracked files in both source and target codebases using `git ls-files`.
2.  **Exclusion Audit**: Explicitly identify and justify excluded files (e.g., application code vs library code).
3.  **Discovery**: Verify if "excluded" files contributed derived content (templates, snippets) to the target.

### 3.2 Phase 2: File Status Classification

1.  **Mapping**: Identify files as `Added`, `Deleted`, `Modified`, or `Binary`.
2.  **Rename Detection**: Correlate renamed file pairs using content similarity to prevent inflated add/delete metrics.
3.  **Category Grouping**: Group added files by architectural layer (Source, Config, CI, Docs, Skills).

### 3.3 Phase 3: Differential Analysis

1.  **Metric Extraction**: Use `git diff --numstat` for modified files to get precise insertion/deletion counts.
2.  **Feature Isolation (Mode B)**: For shared files, count only the lines directly related to the feature (imports, API calls, comments).
3.  **Noise Partitioning**: Explicitly list and exclude unrelated changes (cleanup, reorders, historical logs).

### 3.4 Phase 4: Binary & Artifact Audit

1.  **Non-Source Exclusion**: Block generated files (build outputs, compiled code) from LOC counts.
2.  **Size Reporting**: Report binary files (`.jar`, `.png`, `.zip`) by size (KB/MB), never by line count.

### 3.5 Phase 5: Metric Synthesis & Reporting

1.  **Grand Summary**: Aggregate subtotals for added, deleted, and net change.
2.  **Size Comparison**: Produce a before/after table comparing total file count and source LOC.
3.  **Verification**: Confirm that subtotals match the grand total and that the line-counting method is consistent.

***

## 4. Deep Command Explanation

### 4.1 `git diff --no-index --numstat <file1> <file2>`
- `--no-index`: Allows comparing two files outside of a Git repository or across different repositories.
- `--numstat`: Outputs three columns: `AddedLines`, `DeletedLines`, and `FilePath`.
- This is the authoritative tool for measuring the precise delta between an original file and its derived/renamed version.

### 4.2 `git ls-files`
- Lists all files currently tracked by Git.
- This is used to define the "true scope" of the analysis, ensuring that git-ignored files or untracked local noise are excluded from metrics.

### 4.3 `git log --oneline --stat <baseline>..HEAD`
- `--stat`: Shows a summary of files changed and the number of lines modified per commit.
- Used in Mode B to categorize commits as Full, Partial, or Unscoped feature work.

***

## 5. Prohibited Behaviors

- **No counting generated files**: Build artifacts and compiled code MUST be excluded from source LOC.
- **No line counts for binaries**: Report binary files by size only.
- **No treating renames as add/delete**: Renames MUST be identified and diffed to get the true delta.
- **No silent exclusion**: All excluded files must be listed in the Excluded Files Table with a clear rationale.
- **No double-counting**: Feature lines in shared documents must be counted individually, never as the whole file.

***

## 6. Related Conversations & Traceability

- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
