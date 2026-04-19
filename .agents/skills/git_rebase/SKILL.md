---
name: Git Rebase Standardization
description: Hierarchical multi-branch rebasing with dependency mapping,
    Commit Action Mapping (CAM), dig-down fidelity, and
    operational guardrails.
category: Git & Repository Management
---

# Git Rebase Standardization Skill

> **Skill ID:** `git_rebase`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Git Rebase Standardization Skill

## 1. Scope Statement

This skill establishes the industrial protocol for managing complex, multi-branch Git rebases. It ensures hierarchical alignment, cross-branch commit deduplication, and absolute historical fidelity. The protocol leverages Mermaid dependency mapping, Commit Action Mapping (CAM) tables, and the "Dig Down" content analysis principle to guarantee that rebase operations are predictable, non-regressive, and documented with high technical precision.

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

### 3.1 Phase 1: Hierarchical Dependency Mapping

1.  **Discovery**: Run `git branch -r` to identify default branches and remotes.
2.  **Blueprint**: Construct a Mermaid diagram defining base anchors (e.g., `origin/main`), chain segments (Foundation, Logic, Tooling), and directional rebase paths.

### 3.2 Phase 2: Commit Action Mapping (CAM)

1.  **Categorization**: Assign every commit in the chain to an action: `KEEP`, `REWORD`, `DROP`, or `SQUASH`.
2.  **Deduplication**: Identify and `DROP` redundant commits present in parent branches.
3.  **Authorization Gate**: Present the CAM table to the user for explicit approval.

### 3.3 Phase 3: "Dig Down" Fidelity Audit

1.  **Analysis**: For `REWORD` actions, inspect actual hunks or blobs (e.g., `cat -v`, `jq`).
2.  **Metadata Synthesis**: Construct commit messages that list specific package changes, config keys, or binary assets.

### 3.4 Phase 4: Safety & Baseline Initialization

1.  **Backup**: Create `backup/pre-rebase-<n>` using incremental naming.
2.  **Alignment**: Run `git fetch origin` to ensure synchronization.

### 3.5 Phase 5: Rebase Execution

1.  **SSOT Command**: Execute `git rebase --onto <newbase> <upstream> <branch>` or `git rebase -i`.
2.  **Guardrail**: Never skip empty commits (`git rebase --skip`) without user confirmation.

### 3.6 Phase 6: Verification & Cleanup

1.  **Tree Parity**: Run `git diff HEAD <backup-branch>` to ensure no content was lost.
2.  **Graph Audit**: Verify the resulting history via `git log --oneline --graph`.
3.  **Hygiene**: Perform `git gc --prune=now` and request authorization to delete backups.

***

## 4. Deep Command Explanation

### 4.1 `git rebase --onto <newbase> <upstream> <branch>`
- `--onto <newbase>`: The new base for the branch.
- `<upstream>`: The old base (the commits to be excluded).
- `<branch>`: The branch being rebased.
- This command allows "transplanting" a segment of history from one base to another, effectively skipping intermediate base commits that may have changed.

### 4.2 `git log --oneline --graph`
- Provides a compact, visual representation of the commit graph.
- Critical for verifying that the hierarchical rebase correctly aligned branches according to the Mermaid blueprint.

### 4.3 `git gc --prune=now`
- `gc`: Garbage collection.
- `--prune=now`: Immediately removes unreachable objects from the repository.
- Used for repository hygiene after large history rewrites to keep the `.git` directory lean and optimized.

***

## 5. Prohibited Behaviors

- **No rebasing without backups**: Backup branches are mandatory for all destructive operations.
- **No silent skipping**: Every `git rebase --skip` requires a user authorization gate.
- **No generic rewording**: Commit messages must use "Dig Down" fidelity for specific change lists.
- **No force-moving backups**: Never use `git branch -f` for backups; use incremental naming.
- **No assuming branch names**: Always discover the default branch programmatically.

***

## 6. Related Conversations & Traceability

- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)

