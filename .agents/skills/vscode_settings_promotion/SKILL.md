---
name: VS Code Settings Promotion
description: Automate migration of profile-specific settings to global scope with universal enforcement.
category: VSCode-Configuration
---

# VS Code Settings Promotion Skill

## 1. Scope Statement

This skill establishes the industrial protocol for migrating profile-specific VS Code settings to the global configuration scope with universal enforcement. It ensures that targeted settings are surgically extracted from local `settings.json` profiles, injected into the global `settings.json`, and registered within the `workbench.settings.applyToAllProfiles` array. The protocol mandates atomic backups, JSON syntax validation, and synchronization with source-of-truth repositories (e.g., `configurations-private`) to maintain absolute configuration integrity.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `python3` | 3.x+ | `python3 --version` |
| `jq` | Latest | `jq --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into five operational phases.

### 3.1 Phase 1: Target Identification & Backup

1.  **Selection**: Identify specific settings keys in the profile `settings.json` for promotion.
2.  **Atomic Backup**: Create `.bak` copies of both profile and global `settings.json` files before any modification.
3.  **Value Extraction**: Read and preserve the exact values (including complex objects/arrays) from the source profile.

### 3.2 Phase 2: Global Injection & Enforcement

1.  **Injection**: Insert the extracted settings into the global `settings.json`.
2.  **Universal Registry**: Append the keys to the `workbench.settings.applyToAllProfiles` array to ensure they override all other profiles.
3.  **Deduplication**: Verify that no duplicate keys are created in the global file or the enforcement list.

### 3.3 Phase 3: Profile Cleanup

1.  **Purge**: Remove the promoted settings from the original profile `settings.json` to prevent configuration shadowing.
2.  **Syntax Check**: Validate that both JSON files remain well-formed using `jq`.

### 3.4 Phase 4: Synchronization

1.  **VCS Alignment**: If a `configurations-private` or similar repository is used as a source of truth, synchronize the changes to the tracked versions.
2.  **Commit Protocol**: Use the [Git Atomic Commit Skill](../git_atomic_commit/SKILL.md) to stage and commit the configuration changes.

### 3.5 Phase 5: Verification

1.  **Syntax Validation**: Run `jq . <settings.json>` to confirm perfect JSON structural integrity.
2.  **Functional Verification**: Confirm that the settings are absent from the profile but active in the global scope.

***

## 4. Deep Command Explanation

### 4.1 `python3 scripts/promote.py`
- Executes the industrial-grade promotion logic, handling backup creation, JSON parsing, and surgical key migration.
- Includes a `--dry-run` flag for safety-first verification of proposed changes.

### 4.2 `jq . <file>`
- A command-line JSON processor used here primarily for syntax validation.
- An empty return status (exit code 0) indicates the `settings.json` is syntactically valid after manual or script-based edits.

### 4.3 `workbench.settings.applyToAllProfiles`
- A special VS Code setting that defines which global keys should be enforced across every defined profile.
- Registration here is the final step in the promotion protocol to ensure universal consistency.

***

## 5. Prohibited Behaviors

- **No skipping backups**: Never modify a `settings.json` without an active `.bak` file.
- **No silent failures**: Every JSON modification must be followed by a syntax validation check.
- **No data loss**: Do NOT remove unrelated settings during the promotion process.
- **No manual duplication**: Always use the automated script for multi-key promotions to avoid manual editing errors.
- **No ignoring the source of truth**: Changes must be reflected in the configuration repository if one exists.

***

## 6. Related Conversations & Traceability

- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
- **VS Code Extension Portability**: [../vscode_extension_portability/SKILL.md](../vscode_extension_portability/SKILL.md)
