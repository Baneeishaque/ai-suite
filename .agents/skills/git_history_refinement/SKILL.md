---
name: Git History Refinement
description: Refine or reconstruct existing commit history using backup
    branches, atomic extraction, tree parity verification,
    and safe remote push reconciliation.
category: Git & Repository Management
---

# Git History Refinement Skill

> **Skill ID:** `git_history_refinement`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Git History Refinement Skill

## 1. Scope Statement

This skill establishes the industrial protocol for reconstructing and refining existing Git history. It is used to decompose non-atomic commits (mixed concerns) into logical, independent units through a "Reset and Restore" strategy. The protocol prioritizes historical integrity through mandatory backup branches, tree parity verification, and surgical metadata preservation. It ensures that the final refined history is buildable, clean, and synchronized with remote baselines.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| `jq` | Latest | `jq --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into seven operational phases.

### 3.1 Phase 1: Safety & Backup Protocol

1.  **State Preservation**: Stage all local changes and create a temporary "state preservation" commit.
2.  **Incremental Backup**: Create `backup/pre-refinement-<n>` using incremental naming. Do NOT use `-f`.
3.  **Baseline Verification**: Confirm the target "clean" baseline commit.

### 3.2 Phase 2: Remote Reconciliation

1.  **Fetch**: Run `git fetch origin <branch>`.
2.  **Alignment**: If remote has diverged, identify the latest remote commit as the refinement baseline.

### 3.3 Phase 3: Baseline Reset (Reconstruction)

1.  **Hard Reset**: Run `git reset --hard <clean-commit-hash>`.
2.  **Orphan Root**: Use `git checkout --orphan temp-master` if refining the root commit.

### 3.4 Phase 4: Atomic Extraction & Synthesis

1.  **Standard Restoration**: Use `git show <backup-branch>:<path> > <path>` to restore files.
2.  **JSON Manipulation**: Use `jq` for key-by-key splitting of complex configuration files (e.g., `settings.json`).
3.  **Micro-Renumbering**: Maintain structural validity in protocol files (Phase 1, 2, 3...) at every intermediate commit.

### 3.5 Phase 5: Metadata Preservation & Commit

1.  **Metadata Reuse**: Use `git commit -C <original-hash>` to preserve original timestamps and messages where appropriate.
2.  **Atomic Rationale**: Update commit messages to reflect the refined, atomic scope.

### 3.6 Phase 6: Tree Parity Verification

1.  **Global Diff**: Run `git diff <current-branch> <backup-branch>`.
2.  **Constraint**: The diff **MUST** be empty. Tree parity is the ultimate proof of a non-regressive refinement.

### 3.7 Phase 7: Remote Push Protocol

1.  **Remote Backup**: Create `backup/pre-force-push-<n> origin/<branch>`.
2.  **Authorization**: Ask "Shall I push these changes?".
3.  **Execution**: Use `git push --force-with-lease` ONLY after explicit approval.

***

## 4. Deep Command Explanation

### 4.1 `git reset --hard <hash>`
- Resets the current branch to point to `<hash>` and updates the working tree and index to match.
- This is the "baseline" command for reconstruction, clearing out the messy history segment.

### 4.2 `jq '{ "key": .["key"] }' input.json > output.json`
- `jq`: A command-line JSON processor.
- `{ "key": .["key"] }`: A filter that constructs a new JSON object containing only the specified key from the input.
- This is critical for splitting a large JSON change (e.g., adding 10 rules) into 10 atomic commits.

### 4.3 `git diff <branch1> <branch2>`
- Compares the state of two branches at their respective HEADs.
- In this protocol, it is used to ensure that the reconstructed history (refined) resulted in the exact same final state as the original (backup).

***

## 5. Prohibited Behaviors

- **No auto-deletion**: Never delete backup branches without explicit user authorization.
- **No force-moving**: Avoid `git branch -f`; use incremental naming for backups.
- **No skipping parity checks**: Tree parity is mandatory for all reconstructions.
- **No generic messages**: Commit messages must be evidence-based and specific.
- **No silent force-pushing**: Always use `--force-with-lease` and wait for user approval.

***

## 6. Related Conversations & Traceability

- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Git Rebase Standardization**: [../git_rebase/SKILL.md](../git_rebase/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
