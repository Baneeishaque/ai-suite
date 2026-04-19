---
name: Underscore Naming Convention
description: Enforce underscore_based naming for all project files, directories, and identifiers — with industry-standard exemptions.
category: Naming & Conventions
---

# Underscore Naming Convention Skill

> **Skill ID:** `underscore_naming`
> **Version:** 1.1.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

## 1. Scope Statement

This skill establishes the industrial protocol for enforcing `underscore_based` naming across all project files, directories, artifact identifiers, and internal references. It mandates the detection of naming violations, classification of tool-mandated exemptions, and surgical history-preserving renames via `git mv`. The protocol enforces a "Zero Stale Reference" policy, requiring full blast-radius audits and updates to documentation, build configs, and CI/CD pipelines to maintain perfect technical fidelity.

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

### 3.1 Phase 1: Violation Audit & Inventory

1.  **Inventory Discovery**: Run `git ls-files` to identify the authoritative list of tracked artifacts.
2.  **Violation Detection**: Scan for hyphens, camelCase, or mixed casing in author-chosen names.
3.  **Exemption Classification**: Match findings against the Industry-Standard Exemptions Table.

### 3.2 Phase 2: Blast Radius Analysis

1.  **Grep Audit**: Perform a full-codebase search (`git ls-files | xargs grep`) for every violation target.
2.  **Cross-Reference Mapping**: Document every instance where the old name appears in documentation, build scripts, or source code.
3.  **`.gitignore` Check**: Specifically audit `.gitignore` for patterns or negation rules (`!`) referencing the target names.

### 3.3 Phase 3: Ordered Execution (Deepest-First)

1.  **Renaming Strategy**: Execute `git mv` for all tracked renames.
2.  **Order of Operations**: Rename files first, then directories, proceeding from the deepest paths to the root.
3.  **Fallback (Locked/Untracked)**: Use mirror-and-remove strategies for locked directories or `Rename-Item` for untracked empty shells.

### 3.4 Phase 4: Reference Synchronization

1.  **Atomic Updates**: Update all documented cross-references simultaneously with the rename.
2.  **Artifact ID Sync**: Ensure Maven `<artifactId>`, npm `name`, and Gradle `rootProject.name` are synchronized with the filename.

### 3.5 Phase 5: Zero-Stale Verification

1.  **Verification Grep**: Run a final scan to ensure zero occurrences of the old name remain in the repository.
2.  **Compliance Audit**: Re-run the hyphen detection on `git ls-files` to confirm 100% adherence.

### 3.6 Phase 6: Reporting

1.  **Inventory Report**: Present a final table of renamed items and updated files.
2.  **VCS Status**: Confirm that all changes are staged and ready for an atomic commit.

***

## 4. Deep Command Explanation

### 4.1 `git ls-files | Where-Object { $_ -match '-' }`
- Combines Git's list of tracked files with a regular expression filter for hyphens.
- This is the authoritative way to find naming violations because it ignores local noise (e.g., `node_modules`, `target/`) and focuses only on what is committed to history.

### 4.2 `git mv <old> <new>`
- Moves or renames a file while updating the Git index.
- Mandatory for industrial renaming to preserve file history, attribution (blame), and to avoid repository bloat from delete/add operations.

### 4.3 `git ls-files | xargs grep -n "old-name"`
- Efficiently searches for strings only within files tracked by the current repository.
- Essential for blast radius analysis to ensure no references are broken by the naming change.

***

## 5. Prohibited Behaviors

- **No renaming of exempt files**: Do NOT touch tool-mandated names like `package-lock.json` or `.gitignore`.
- **No partial updates**: Never rename a file without updating every single reference identified in the blast radius.
- **No renaming of rule files**: Files in `ai-agent-rules/` are exempt (kebab-case mandated).
- **No guessing**: If a search pattern returns ambiguous results, pause and ask for clarification.
- **No assuming**: Verification (Step 5) is mandatory; never assume a rename was successful without scanning.

***

## 6. Related Conversations & Traceability

- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
- **Project Structure**: [../project_structure/SKILL.md](../project_structure/SKILL.md)
- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
