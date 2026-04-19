---
name: Maven POM Audit
description: Section-by-section audit of Maven pom.xml files — catching invalid URLs, wrong identities, missing metadata, and enforcing placeholder conventions.
category: Build & Dependency Management
---

# Maven POM Audit Skill

> **Skill ID:** `maven_pom_audit`
> **Version:** 1.0.0
> **Standard:** [Agent Skills (agentskills.io)](https://agentskills.io)

# Maven POM Audit Skill

## 1. Scope Statement

This skill establishes the industrial protocol for auditing and hardening Maven `pom.xml` files. It provides a systematic, section-by-section audit to detect invalid URLs, stale developer identities, missing mandatory metadata, and unpinned dependencies. The protocol enforces the `YOUR_*` placeholder convention for unconfigured targets, ensuring that POM files remain valid XML while clearly marking configuration gaps. This approach prioritizes build reproducibility, metadata integrity, and pedagogical clarity.

***

## 2. Environment & Dependencies

Before execution, the agent MUST verify the following:

| Requirement | Minimum Version | Verification Command |
|---|---|---|
| `mvn` | 3.6+ | `mvn -version` |
| `java` | 8+ | `java -version` |
| `git` | 2.x+ | `git --version` |
| Shell | PowerShell 5.1+ or Bash 4+ | `$PSVersionTable` or `bash --version` |

***

## 3. Protocol Layers

The protocol is organized into seven operational phases.

### 3.1 Phase 1: Global Discovery

1.  **Inventory**: Find every `pom.xml` in the project, excluding noise directories (`target`, `.git`, `node_modules`).
2.  **Indexing**: List all discovered POMs with their relative paths.

### 3.2 Phase 2: Metadata & Identity Audit

1.  **GAV Validation**: Check `<groupId>`, `<artifactId>`, and `<version>` for naming conventions and SemVer compliance.
2.  **Project Metadata**: Verify presence of `<name>`, `<description>`, and `<url>`.
3.  **Developer Identity**: Derive actual author details from `git config user.name` and `git config user.email` and compare against the `<developers>` block.
4.  **Licensing**: Ensure at least one `<license>` block with a valid name and URL exists.

### 3.3 Phase 3: URL & SCM Hardening

1.  **URL Validation**: Audit every URL (Project, SCM, Distribution Management) for reachability.
2.  **Placeholder Application**: Replace invalid/placeholder URLs with the `YOUR_*` convention (e.g., `YOUR_VCS_HOST`) and add `<!-- TODO -->` comments.

### 3.4 Phase 4: Build Reproducibility Audit

1.  **Encoding**: Ensure `<project.build.sourceEncoding>` is explicitly set to `UTF-8`.
2.  **Compiler**: Verify `<maven.compiler.source>` and `<maven.compiler.target>` are explicit and matching.
3.  **Timestamp**: Ensure `<project.build.outputTimestamp>` exists for Maven 3.9+ to enable reproducible builds.

### 3.5 Phase 5: Dependency & Plugin Pinning

1.  **Version Audit**: Verify that every `<dependency>` and `<plugin>` has an explicit `<version>` (or inherits from a pinned BOM).
2.  **Snapshot Audit**: Scan for `-SNAPSHOT` dependencies in non-snapshot project versions.
3.  **Property Extraction**: Ensure versions are extracted to the `<properties>` section for multi-use dependencies.

### 3.6 Phase 6: Cascading Reference Verification

1.  **Sync Audit**: Check CI/CD configs (`*-pipelines.yml`), IDE configs (`.project`), and documentation (`README.md`) for stale references to POM values (Artifact IDs, URLs).
2.  **Registry Sync**: Ensure all agent skills and references match the audited coordinates.

### 3.7 Phase 7: Verification & Parsing

1.  **Validation**: Run `mvn validate -q` to ensure the POM is syntactically correct.
2.  **Placeholder Scan**: Run a final `grep` or `Select-String` for `YOUR_` to provide a summary of remaining configuration tasks.

***

## 4. Deep Command Explanation

### 4.1 `mvn validate -q`
- `validate`: Validates that the project is correct and all necessary information is available.
- `-q` (or `--quiet`): Suppresses all but error messages.
- This is the fastest way to confirm that the XML structure and Maven model are sound after manual edits.

### 4.2 `git config user.name` / `git config user.email`
- These commands retrieve the local Git identity of the current user.
- This is the authoritative source for the `<developers>` section, preventing stale template data from persisting in the POM.

### 4.3 `Select-String -Path pom.xml -Pattern "SNAPSHOT"`
- Searches the POM for any instance of "SNAPSHOT".
- Critical for release-readiness audits, as release builds should typically never depend on snapshot versions.

***

## 5. Prohibited Behaviors

- **No deleting sections**: Audit fixes replace values or add placeholders; they NEVER remove existing blocks.
- **No inventing identities**: Never guess developer names or emails; derive them from Git or ask the user.
- **No inventing URLs**: Use `YOUR_*` placeholders for unknown targets; never provide fake URLs that might 404.
- **No auto-deploying**: Audit only; never run `mvn deploy` or other destructive build goals during an audit.
- **No ignoring side-effects**: A POM change is incomplete without checking CI/CD and documentation for stale references.

***

## 6. Related Conversations & Traceability

- **Standardization Rules**: [ai-rule-standardization-rules.md](../../../ai-agent-rules/ai-rule-standardization-rules.md)
- **Project Structure**: [../project_structure/SKILL.md](../project_structure/SKILL.md)
- **Git Atomic Commit**: [../git_atomic_commit/SKILL.md](../git_atomic_commit/SKILL.md)
