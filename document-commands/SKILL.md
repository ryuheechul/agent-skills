---
name: document-commands
description: Enforces a 'Document-then-Execute' workflow. Use when an agent needs to run shell commands, execute tests, build projects, or perform any task that should favor established task runners (Makefile, npm run) and be logged to .cmds-by-agents/ for auditability.
---

# Document Commands

This skill guides agents to prioritize project-specific, established commands and ensures that non-trivial command executions are documented in a dedicated directory for auditability.

## Core Mandates

### 1. Prioritize Established Commands
Before executing any command to build, test, lint, or run the project, you **SHOULD** first research the repository for established task runners or scripts.

- **Check for**: `package.json`, `Makefile`, `tasks.json`, `Justfile`, `Taskfile.yml`, or scripts in `scripts/`, `bin/`, or `tools/`.
- **Action**: If an established command exists (e.g., `npm run test` instead of `vitest`), you **SHOULD** prefer it.
- **Nuance**: Standard, simple calls to established task runners (e.g., `npm run test`, `make build`) are considered safe and do not strictly require the creation of a documented script unless they are being modified or are part of a larger sequence.

### 2. Document Non-Trivial Command Executions
To ensure auditability and persistence, **AVOID** executing bespoke or complex commands directly. Instead, follow this "Document-then-Execute" workflow for non-trivial tasks.

#### Workflow: Document-then-Execute
1.  **Preparation**: Ensure the `.cmds-by-agents/` directory exists (e.g., `mkdir -p .cmds-by-agents`).
2.  **Creation**: Use `write_file` or an equivalent tool to create a shell script in `.cmds-by-agents/`.
    - **Naming Convention**: `<purpose>-<YYYYMMDD>-<HHMMSS>.sh`.
    - **Content**:
      ```bash
      #!/usr/bin/env bash
      set -x
      [Full command here]
      ```
3.  **Permissions**: Make the script executable (`chmod +x`).
4.  **Execution**: Run the generated script directly (e.g., `./.cmds-by-agents/script.sh`).

#### Exceptions
- Simple research, navigation, and management commands (e.g., `ls`, `cd`, `mkdir`, `grep`, `git status`) are allowed to be run directly.
- Standard, direct calls to established project scripts (e.g., `npm start`) may be run directly if they don't involve complex flags or environment setup.

### 3. Security & Secrets
- **No Secrets**: NEVER embed sensitive information (API keys, passwords, tokens) in the command script.
- **Execution-Time Secrets**: Sensitive environment variables must be provided at execution time, either by assuming they are provided by external tooling or by confirming with the user.
- **No Leakage**: If secrets are being handled, **DO NOT** use `set -x` in the script to avoid leaking values in the output.

#### Source Control
- Do **NOT** commit the `.cmds-by-agents/` directory unless explicitly instructed. It should be ignored in `.gitignore`.

## Example Workflow

1.  **Research**: You find `"test": "vitest"` in `package.json`.
2.  **Simple Action**: You may run `npm run test` directly.
3.  **Bespoke Action**: If you need to run tests with specific filters and a custom reporter:
    - Create `.cmds-by-agents/test-filtered-20260312-143005.sh`:
      ```bash
      #!/usr/bin/env bash
      set -x
      npm run test -- --filter="auth" --reporter=verbose
      ```
    - `chmod +x .cmds-by-agents/test-filtered-20260312-143005.sh`
    - `./.cmds-by-agents/test-filtered-20260312-143005.sh`
