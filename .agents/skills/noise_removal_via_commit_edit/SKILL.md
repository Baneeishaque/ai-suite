---
name: IDE Noise Removal via Commit Edit
description: Detect and remove IDE artifact noise (m2e, JDT LS,
    filteredResources) from existing commits using the commit_edit
    skill, with mandatory user confirmation.
category: Git & Repository Management
---

# IDE Noise Removal via Commit Edit Skill

> **Skill ID:** `noise_removal_via_commit_edit`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# IDE Noise Removal via Commit Edit Skill

## 1. Scope Statement

This skill establishes the industrial protocol for detecting and removing IDE artifact noise from existing Git history. It utilizes the `commit_edit` skill as the execution backbone to surgically purge boilerplate injected by JDT Language Server, m2e, and other IDE tooling from `.project`, `.classpath`, and `.settings/` files. The protocol enforces mandatory noise classification, attribution accuracy, and user authorization gates to ensure that functional changes are preserved while repository hygiene is restored.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `git` | 2.x+ | `git --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |
| Skill | `commit_edit` | `view_file <path_to_commit_edit/SKILL.md>` |

***

## 3. Protocol Layers

The protocol is organized into five operational phases.

### 3.1 Phase 1: Noise Classification & Attribution

1.  **Commit Inspection**: Run `git show --stat <commit>` to identify suspicious metadata modifications.
2.  **Separation**: Partition files into `Noise` (IDE boilerplate) and `Functional` (feature logic) categories.
3.  **Attribution**: Correctly identify the source (e.g., JDT Language Server m2e injection) to provide pedagogical context.

### 3.2 Phase 2: Authorization Gate (Mandatory)

1.  **Noise Report**: Present a categorized table of noise vs functional files to the user.
2.  **Impact Analysis**: Explicitly warn if removing metadata might break shared workspace configurations.
3.  **Explicit Consent**: Obtain a `yes` or `no` before initiating any history-altering operations.

### 3.3 Phase 3: Surgical Execution

1.  **Interactive Rebase**: Initiate `git rebase -i <commit>~1` and mark the target as `edit`.
2.  **Restoration**: Restore noise files to their state in the parent commit using `git checkout HEAD~1 -- <file>`.
3.  **Mixed-File Handling**: If a file contains both noise and functional changes, use hunk-based staging to preserve the latter.

### 3.4 Phase 4: Fidelity Verification

1.  **Staged Audit**: Run `git diff --cached --stat` to ensure only functional changes remain.
2.  **Amending**: Finalize the edit with `git commit --amend --no-edit` (or reword if needed).
3.  **Replay**: Complete the rebase and verify successful descendant replay.

### 3.5 Phase 5: Post-Edit Hygiene

1.  **Tree Parity**: Run a final `git show --stat` on the new commit and compare against the pre-edit functional list.
2.  **Graph Verification**: Confirm the new commit is correctly integrated into the history graph.
3.  **Reporting**: Present a final summary of modified hashes and removed files.

***

## 4. Deep Command Explanation

### 4.1 `git show --stat <commit> -- ':!*.project' ':!*.classpath'`
- `:!*.project`: The `!` prefix in the pathspec excludes files matching the pattern.
- This command isolates the functional changes in a commit by filtering out known IDE noise patterns, allowing for a focused audit.

### 4.2 `git checkout HEAD~1 -- <path>`
- Restores the file at `<path>` to its state as of the parent commit (`HEAD~1`).
- This is the standard mechanism for "undoing" changes to specific files within an interactive rebase session before amending.

### 4.3 `git diff --name-only <commit>~1 <commit> -- "*.settings/*"`
- Identifies all files modified within the `.settings/` directory in a specific commit.
- Used for rapid noise discovery without the overhead of viewing full diffs.

***

## 5. Prohibited Behaviors

- **No auto-removal**: Never remove noise without presenting a Noise Report and obtaining user authorization.
- **No bulk deletion of mixed files**: If a `.project` file contains functional settings, use `git add -p` for surgical removal.
- **No misattribution**: Never claim `.project` changes come from `vscjava.vscode-maven`; they are injected by `eclipse.jdt.ls`.
- **No batch editing**: Edit one commit per rebase session to minimize conflict risk and maintain atomic history control.
- **No skipping parity checks**: Always verify that the functional line counts match the pre-edit state.

***

## 6. Related Conversations & Traceability

- **Git Commit Edit**: [../commit_edit/SKILL.md](../commit_edit/SKILL.md)
- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **Git History Refinement**: [../git_history_refinement/SKILL.md](../git_history_refinement/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
