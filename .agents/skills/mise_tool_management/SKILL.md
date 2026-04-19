---
name: Mise Tool Management
description: Industrial protocols for mise configuration trust, tool version selection, and Python package setup.
category: Environment-Management
---

# Mise Tool Management Skill

> **Skill ID:** `mise_tool_management`
> **Version:** 1.1.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

## 1. Scope Statement

This skill establishes the industrial protocol for managing `mise`-based development environments. It covers configuration trust, tool version selection, Python interpreter verification, and automated package setup. It ensures that development environments are reproducible, isolated, and explicitly authorized by the user.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the availability of the following tools:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `mise` | Latest stable | `mise --version` |
| `curl` | Any | `curl --version` |
| `jq` | Any | `jq --version` |
| `taplo` | Latest stable | `taplo --version` |

***

## 3. Protocol Layers

The protocol is organized into four hierarchical layers.

### 3.1 Phase 1: Configuration Trust Protocol

`mise` requires explicit trust for any configuration file (`mise.toml`).

1.  **Detection**: Run `mise ls 2>&1` and check for "not trusted" errors.
2.  **Analysis**: Read and present the `mise.toml` content to the user.
3.  **Authorization**: Ask: "Do you want to trust this `mise.toml`? (yes / no)".
4.  **Execution**: `mise trust path/to/mise.toml`.

### 3.2 Phase 2: Tool Selection Protocol

Ensures the correct tool versions are installed and used.

1.  **Inventory**: `mise ls <tool> --json`.
2.  **Freshness Check**: `mise ls-remote <tool> | tail -5`.
3.  **Comparison**: If `installed` > `required`, ask user to use the installed version and update `mise.toml`.
4.  **Selection**: `mise use --path . <tool>@<version>`.

### 3.3 Phase 3: Python Environment Setup

Specialized verification for Python environments.

1.  **Verification**: `mise exec -- python --version`.
2.  **Pip Check**: `mise exec -- python -m pip --version`.
3.  **Repair**: If missing, run `mise exec -- python -m ensurepip --upgrade`.

### 3.4 Phase 4: Python Package Setup Protocol

Automated dependency management via `pip`.

1.  **Requirements Detection**: Check for `requirements.txt`.
2.  **Freshness Check**: `curl -s https://pypi.org/pypi/<package>/json | jq -r '.info.version'`.
3.  **Decision**: Offer to update `requirements.txt` to the latest version.
4.  **Installation**: `mise exec -- python -m pip install -r requirements.txt`.

***

## 4. Deep Command Explanation

### 4.1 `mise exec -- <command>`
- `mise exec`: Runs the command within the context of the `mise` environment.
- `--`: Separates `mise` arguments from the command to be executed.
- This is the mandatory way to invoke tools in a `mise`-managed project to ensure correct pathing and versioning.

### 4.2 `taplo check`
- Validates TOML syntax for `mise.toml`.
- Critical for ensuring configuration integrity before trust is applied.

### 4.3 `mise ls --json`
- Retrieves tool status in a machine-readable format.
- Essential for automated version audits and comparison against required baselines.

***

## 5. Prohibited Behaviors

- **No auto-trust**: Never run `mise trust` without showing the config and getting approval.
- **No global pollution**: Never run `mise use` without `--path` to a local config.
- **No path-based invocation**: Never call `python` or `pip` directly; always use `mise exec`.
- **No silent updates**: Never update `requirements.txt` without showing the version delta to the user.

***

## 6. Related Conversations & Traceability

- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
- **System-Wide Tool Management**: [../system_wide_tool_management/SKILL.md](../system_wide_tool_management/SKILL.md)
