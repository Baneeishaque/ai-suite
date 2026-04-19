---
name: Git Atomic Commit Construction
description: Analyze, group, and arrange working-tree changes into logical,
    independent atomic commits — with hunk-based staging, formatting
    isolation, and mandatory user authorization.
category: Git & Repository Management
---

# Git Atomic Commit Construction Skill

> **Skill ID:** `git_atomic_commit`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Git Atomic Commit Construction Skill

## 1. Scope Statement

This skill establishes the high-fidelity protocol for constructing atomic, logically coherent Git commits from working-tree changes. It covers the complete lifecycle from deep dependency analysis and logical grouping to hunk-based staging, formatting isolation, and mandatory user authorization. This protocol ensures that every commit is independent, buildable, and descriptively documented, preventing mixed concerns and historical noise.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| `gh` | Latest | `gh auth status` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into seven operational phases.

### 3.1 Phase 1: Environment & Context Initialization

1.  **Authentication**: Verify GitHub CLI status.
2.  **Targeting**: Identify the correct repository and branch. Ensure not in "detached HEAD" state.
3.  **Synchronization**: Run `git pull` to align with the remote.

### 3.2 Phase 2: Deep Change Analysis

1.  **Inventory**: Run `git status` to detect staged, unstaged, and untracked changes.
2.  **Dependency Mapping**: Identify cross-file references and shared identifiers.
3.  **Authorization Gate**: Present a complete inventory to the user.

### 3.3 Phase 3: Logical Arrangement (Commit Planning)

1.  **Grouping**: Partition changes by functional coupling, separating `style`, `refactor`, and `feat/fix`.
2.  **Independence**: Ensure each proposed commit is a standalone logical unit.
3.  **Preview**: Present the "Arranged Commits Preview" with verbose details and actual diff hunks.

### 3.4 Phase 4: Execution & Hunk-Based Staging

1.  **Interactive Staging**: Use `git add -p` for files with mixed concerns.
2.  **Hygiene**: Discard unintentional noise (whitespace, IDE artifacts) ONLY after user confirmation.
3.  **Verification**: Confirm staged state via `git diff --cached`.

### 3.5 Phase 5: Metadata & Documentation

1.  **Quality Standards**: Construct specific, non-repetitive commit messages explaining the "Why".
2.  **Submodule Sync**: Delegate to the [Git Submodule Commit Details Skill](../git_submodule_commit_details/SKILL.md) for sync commits.

### 3.6 Phase 6: Post-Commit Verification

1.  **Consistency Audit**: Verify that the repository remains in a buildable state.
2.  **History Audit**: Present the refined history via `git log`.

### 3.7 Phase 7: Remote Synchronization

1.  **Authorization Gate**: Ask: "Shall I push these changes?".
2.  **Execution**: Push only after explicit approval.

***

## 4. Deep Command Explanation

### 4.1 `git add -p <file>`
- `-p` (or `--patch`): Launches an interactive session to select specific "hunks" (chunks of changes) for staging.
- **Commands**: `y` (stage), `n` (skip), `s` (split hunk), `q` (quit).
- This is the primary tool for maintaining atomicity when multiple unrelated changes exist in a single file.

### 4.2 `git ls-files --others --exclude-standard`
- `--others`: Lists untracked files.
- `--exclude-standard`: Applies standard Git ignore rules (`.gitignore`, etc.).
- This is the authoritative way to identify new files that are candidates for version control without including noise.

### 4.3 `git checkout -- <file>`
- This command overwrites changes in the working tree with the version from the index (or HEAD).
- Used to discard unintentional noise or rejected hunks after interactive staging, ensuring a clean working state.

***

## 5. Prohibited Behaviors

- **No auto-committing**: Never commit without explicit "start" authorization.
- **No auto-pushing**: Never push without explicit user request.
- **No mixing concerns**: Never combine `style`, `refactor`, and functional logic in one commit.
- **No skipping previews**: The verbose arranged commits display is mandatory.
- **No silent discards**: Never discard IDE artifacts or suspected noise without user confirmation.

***

## 6. Related Conversations & Traceability

- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
- **Git Submodule Commit Details**: [../git_submodule_commit_details/SKILL.md](../git_submodule_commit_details/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
