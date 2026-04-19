---
name: Git Commit Edit
description: Edit existing commits in-place via interactive rebase —
    remove files, add files, amend content, or fix mixed concerns
    without full history reconstruction.
category: Git & Repository Management
---

# Git Commit Edit Skill

# Git Commit Edit Skill

## 1. Scope Statement

This skill establishes the surgical protocol for editing existing Git commits in-place via interactive rebase. It provides a "scalpel" approach for removing unwanted files (noise, binaries), adding missing files, amending content, or correcting metadata (author/email) while preserving the integrity of descendant commits. This protocol prioritizes safety through mandatory stashing, backup branches, and explicit user authorization gates.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

- **State Verification**: A clean working tree is preferred. If dirty, the [Git Atomic Commit Skill](../git_atomic_commit/SKILL.md) or `git stash` protocol MUST be applied.

***

## 3. Protocol Layers

The protocol is organized into five operational phases.

### 3.1 Phase 1: Pre-Edit Analysis & Planning

1.  **Identification**: Confirm target commit hash and descendant count via `git log --oneline -n`.
2.  **Inspection**: Run `git show --stat <hash>` to understand the full footprint.
3.  **Divergence Check**: Verify if the commit has been pushed to remote (`git log <hash>..origin/<branch>`).
4.  **Authorization Gate**: Present a formal "Commit Edit Plan" to the user and obtain explicit "Proceed" confirmation.

### 3.2 Phase 2: Safety Initialization

1.  **Mandatory Stash**: Run `git stash push -m "Pre-edit stash"` if the working tree is dirty.
2.  **Backup Branch**: Create `backup/pre-edit-<n>` to ensure a recovery point.

### 3.3 Phase 3: Interactive Rebase Execution

1.  **Sequence Editor**: Use a non-interactive script to mark the target commit as `edit`.
2.  **Edit Loop**: Perform surgical changes (removal, addition, modification).
3.  **Amending**: Run `git commit --amend --no-edit` (or with `-m` if updating message).
4.  **Continuation**: Run `git rebase --continue` and handle any downstream conflicts.

### 3.4 Phase 4: Remote Synchronization

1.  **Push Authorization**: If the branch has diverged, create a remote backup branch (`backup/pre-force-push-<n>`).
2.  **Execution**: Ask "Shall I push these changes?". Use `git push --force-with-lease` ONLY after explicit approval.

### 3.5 Phase 5: Verification & Cleanup

1.  **Audit**: Present the refined history via `git log`.
2.  **Cleanup Gate**: Ask: "Shall I clean up the backup branches?". Do NOT delete without approval.

***

## 4. Deep Command Explanation

### 4.1 `git checkout HEAD~1 -- <file>`
- `git checkout`: In this context, it restores files in the working tree.
- `HEAD~1`: Points to the state of the file in the commit immediately preceding the current one.
- `-- <file>`: Ensures that `<file>` is treated as a path, preventing ambiguity if a branch shares the same name. This effectively reverts the file's changes in the current commit.

### 4.2 `git commit --amend --author="..."`
- `--amend`: Modifies the most recent commit instead of creating a new one.
- `--author="..."`: Overrides the author metadata for the commit.
- `--no-edit`: Reuses the existing commit message without opening an editor.

### 4.3 `git push --force-with-lease`
- `--force-with-lease`: A safer alternative to `--force`. It checks that the remote ref hasn't been updated by someone else before overwriting it, preventing accidental loss of teammate's work.

***

## 5. Prohibited Behaviors

- **No auto-deletion**: Never delete backup branches without explicit user authorization.
- **No silent pushing**: Never execute `git push` (especially force-push) without a user gate.
- **No skipping backups**: Backup branches are mandatory for all history-rewriting operations.
- **No hard resets**: Avoid `git reset --hard` for targeted file removal; use `git checkout HEAD~1 --` instead.

***

## 6. Related Conversations & Traceability

- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
