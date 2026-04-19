---
name: Project Structure and Documentation
description: Industrial-standard project folder structure, root hygiene, README conventions, and documentation placement rules.
category: Project Organization
---

# Project Structure & Documentation Skill

## 1. Scope Statement

This skill establishes the industrial protocol for organizing project folder structures, root-level hygiene, and documentation placement. It classifies every project artifact as tool-mandated (fixed) or author-chosen (movable), relocates misplaced items using `git mv` to preserve history, and ensures that `README.md` and `AGENTS.md` follow mandatory structural patterns. The protocol prioritizes discoverability, zero duplication, and absolute technical fidelity.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into seven operational phases.

### 3.1 Phase 1: Root Inventory & Classification

1.  **Discovery**: List every file and directory in the project root using `Get-ChildItem -Force`.
2.  **Classification**: Match items against the Root File Classification Table (tool-mandated vs author-chosen).
3.  **Triage**: Identify candidates for relocation (plans, design docs, dormant CI configs).

### 3.2 Phase 2: Directory Normalization

1.  **Initialization**: Create standard directories (`docs/`, `tools/`, `packaging/`, `samples/`, `.launches/`) ONLY if content exists to populate them.
2.  **Move Execution**: Use `git mv` for all relocations to maintain history and blame logs.
3.  **Path Hygiene**: Move deepest paths first to avoid parent-path invalidation.

### 3.3 Phase 3: Blast Radius Analysis

1.  **Reference Audit**: Search the entire codebase for stale references to moved files (implementation plans, executables, config keys).
2.  **Source Sync**: Update source code (`ProcessBuilder`, `Runtime.exec`) that references moved runtime tools.
3.  **Link Verification**: Validate all relative markdown links (`[text](path)`) to ensure they are broken-link free.

### 3.4 Phase 4: README & AGENTS.md Standardization

1.  **README Refactor**: Ensure the 12 mandatory sections (Title, Description, Quick Start, etc.) are present and in order.
2.  **Bridge Creation**: Implement `AGENTS.md` as a thin routing table (maximum ~15 lines) linking to README and SKILL.md.
3.  **Zero Duplication**: Purge any content duplicated across the documentation stack.

### 3.5 Phase 5: Release Engineering Audit

1.  **Artifact Scoping**: Ensure `releases/` contains versioned zips and release notes.
2.  **Version Sync**: Verify that version constants in source code match the release coordinates.
3.  **Dependency Bundling**: Confirm that release artifacts include all runtime JARs and dependencies.

### 3.6 Phase 6: Gitignore Alignment

1.  **Sectioning**: Move all project-specific rules and exceptions to the bottom of `.gitignore` under a clear header.
2.  **Exception Audit**: Whitelist necessary artifacts (e.g., `!.launches/*.launch`) and ignore build outputs (`dist/`).

### 3.7 Phase 7: Final Verification

1.  **Root Hygiene**: Confirm the root contains only convention-approved files.
2.  **Link Fidelity**: Re-run the broken link checker on all MD files.
3.  **VCS Status**: Run `git status` to ensure all moved files are correctly staged and tracked.

***

## 4. Deep Command Explanation

### 4.1 `git mv <old> <new>`
- Renames or moves a file or directory, and stages the change in the Git index.
- This is the mandatory command for restructuring to ensure that Git's heuristic for tracking renames remains accurate and that file history is preserved.

### 4.2 `Get-ChildItem -Path . -Force`
- Lists all files in the current directory, including hidden ones (prefixed with `.`).
- Critical for auditing root-level hygiene, as hidden IDE and tool config files must be correctly classified or relocated.

### 4.3 `git ls-files --others --exclude-standard`
- Identifies untracked files that are not ignored by `.gitignore`.
- Used at the end of normalization to ensure that no "orphaned" files were created during relocation.

***

## 5. Prohibited Behaviors

- **No empty directories**: Do NOT create a directory unless it will immediately contain at least one file.
- **No moving tool-mandated files**: `pom.xml`, `.gitignore`, `Makefile`, etc., MUST remain in the root.
- **No absolute paths**: All links in README, AGENTS.md, and source code MUST be relative.
- **No partial READMEs**: Every `README.md` must contain all mandatory sections.
- **No duplication**: Content must exist in one place only (e.g., README for humans, AGENTS.md for routing, docs/ for details).

***

## 6. Related Conversations & Traceability

- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
