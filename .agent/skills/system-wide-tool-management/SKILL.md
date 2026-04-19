---
name: System-Wide Tool Management
description: Industrial protocol for detecting, installing, and verifying system-wide
    CLI tools (e.g. jq, curl, git) across macOS, Linux, and Windows. Use whenever a
    system tool is required but may not be installed or available in PATH.
category: Environment-Management
---

# System-Wide Tool Management Skill

## 1. Scope Statement

This skill establishes the industrial protocol for detecting, installing, and verifying system-wide CLI tools (e.g., `jq`, `curl`, `git`) across macOS, Linux, and Windows. It ensures that required global binaries are available on the system `PATH` using a hierarchical fallback mechanism through various package managers.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the availability of native shell environment tools:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `uname` | Any | `uname -s` (for OS detection) |
| `which` | Any | `which which` |
| `curl` | Any | `curl --version` |

***

## 3. Protocol Layers

The protocol is organized into three hierarchical layers.

### 3.1 Layer 1: Detection & PATH Verification

Before using any system tool, verify its existence and reachability.

1.  **Detection**: `which <tool> 2>/dev/null && <tool> --version || echo "NOT_FOUND"`.
2.  **OS Identification**: Use `uname -s` to select the appropriate package manager.

### 3.2 Layer 2: Recursive Fallback Installation

If a tool is missing, use the prioritized package manager matrix.

1.  **User Authorization (MANDATORY)**: Show the command and official documentation link. Ask: "Shall I run this? (yes / no)".
2.  **Priority Matrix**:
    - **macOS**: `brew` -> `port` -> Manual.
    - **Linux (Debian/Ubuntu)**: `apt-get` -> `snap` -> Manual.
    - **Linux (RHEL/Fedora)**: `dnf` -> `yum` -> Manual.
    - **Windows**: `scoop` -> `winget` -> `choco` -> Manual.
3.  **Sudo Notification**: Explicitly warn the user if a command requires `sudo`.

### 3.3 Layer 3: Post-Install Verification

Confirm the tool is reachable on `PATH` after installation.

1.  **Verification**: `which <tool> && <tool> --version`.
2.  **Path Refresh**: If missing, attempt to source shell profiles or refresh environment variables (e.g., `eval "$(/opt/homebrew/bin/brew shellenv)"`).

***

## 4. Deep Command Explanation

### 4.1 `which <tool>`
- `which`: Locates the executable file associated with a given command by searching the directories listed in the `PATH` environment variable.
- `2>/dev/null`: Redirects standard error to the null device, suppressing "not found" messages.

### 4.2 `brew shellenv`
- `eval "$(/opt/homebrew/bin/brew shellenv)"`: Executes the shell commands output by `brew shellenv`, which set up necessary environment variables (PATH, MANPATH, etc.) for Homebrew on Apple Silicon.

***

## 5. Execution Protocol

1.  **Detect Tool**: Check if the required tool is on `PATH`.
2.  **Identify OS**: Determine the current operating system.
3.  **Propose Installation**: Select the best package manager and ask for approval.
4.  **Execute Install**: Run the authorized command with full interactive output.
5.  **Verify Path**: Confirm reachability and execute a version check.

***

## 6. Prohibited Behaviors

- **No silent installs**: Never run an installation command without explicit user confirmation.
- **No silent sudo**: Never use `sudo` without explicitly notifying the user.
- **No auto-accept flags**: Do NOT use `-y` or `--noconfirm`. Show full output for review.
- **No scope confusion**: Do NOT use this skill for project-local tools (use `mise`, `pip`, or `npm` instead).

***

## 7. Related Conversations & Traceability

- **Mise Tool Management**: [.agent/skills/mise-tool-management/SKILL.md](../mise-tool-management/SKILL.md)
- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
