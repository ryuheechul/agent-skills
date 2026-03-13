---
name: document-commands
description: Enforces a 'Document-then-Execute' workflow. Use when an agent needs to run shell commands, execute tests, build projects, or perform any task that should favor established task runners (Makefile, npm run) and be logged to .cmds-by-agents/ for auditability.
---

# Document Commands

This skill guides agents to use project-specific, established commands instead of ad-hoc one-liners, and ensures every command execution is documented in a dedicated directory.

## Core Mandates

### 1. Favor Established Commands
Before executing any command to build, test, lint, or run the project, you **MUST** first research the repository for established task runners or scripts.

- **Check for**:
  - `package.json` (npm/yarn/pnpm scripts)
  - `Makefile` or `makefile`
  - `tasks.json` (VS Code tasks)
  - `Justfile` (Just runner)
  - `Taskfile.yml` (Task runner)
  - Shell scripts in `scripts/`, `bin/`, or `tools/`
- **Action**: If an established command exists (e.g., `npm run test` instead of `vitest`), you **MUST** use the established one.

### 2. Document All Command Executions
To ensure auditability and persistence across agent sessions, do **NOT** execute commands directly. Instead, follow this "Document-then-Execute" workflow.

#### Workflow: Document-then-Execute
1.  **Preparation**: Ensure the `.cmds-by-agents/` directory exists at the project root (e.g., `mkdir -p .cmds-by-agents`).
2.  **Creation**: Use `write_file` or an equivalent file-writing tool (e.g., `sed`, `tee`, or any tool that provides a visible diff/output of the file content) to create a shell script in `.cmds-by-agents/`.
    - **Visibility**: Using a file-writing tool ensures the command content is clearly visible in the tool output/diff for review.
    - **Naming Convention**: `<purpose>-<YYYYMMDD>-<HHMMSS>.sh` (e.g., `run-tests-20260312-143005.sh`).
    - **Content**:
      ```bash
      #!/bin/bash
      set -x
      # Command: [Full command here]
      [Full command here]
      ```
3.  **Permissions**: Make the script executable (e.g., `chmod +x .cmds-by-agents/run-tests-20260312-143005.sh`).
4.  **Execution**: Execute the command by running the generated script directly (e.g., `./.cmds-by-agents/run-tests-20260312-143005.sh`).

### 3. Security & Environment Variables
- **No Secrets**: NEVER embed sensitive information (API keys, passwords, tokens) directly in the command script.
- **Sensitive Env Vars**: If a command requires sensitive environment variables, they must be provided at execution time (e.g., `API_KEY=xxx ./.cmds-by-agents/deploy.sh`) rather than being written into the script.
- **Avoid Leakage**: When sensitive information is being passed or handled, **DO NOT** use `set -x` or any echoing that would reveal secret values in the output.
- **Non-Sensitive Vars**: Embedding non-sensitive environment variables (e.g., `NODE_ENV=production`) in the script is encouraged for clarity and reproducibility.

#### Exceptions
- Simple research/management commands like `mkdir`, `ls`, `grep`, or `cd` are allowed to be run directly.
- Do **NOT** commit the `.cmds-by-agents/` directory unless explicitly instructed. It should be ignored in `.gitignore`.

## Example Workflow

If you want to run Vitest tests:

1.  **Research**: You find `"test": "vitest"` in `package.json`.
2.  **Creation**: Use `write_file` to create `.cmds-by-agents/test-20260312-143005.sh`:
    ```bash
    #!/bin/bash
    set -x
    # Command: npm run test
    npm run test
    ```
3.  **Permissions**: `run_shell_command("chmod +x .cmds-by-agents/test-20260312-143005.sh")`.
4.  **Execution**: `run_shell_command("./.cmds-by-agents/test-20260312-143005.sh")`.
