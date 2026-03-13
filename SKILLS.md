# Agent Skills Repository

This repository provides specialized agent skills for the Gemini CLI and other compatible agents.

## Project Goal
To provide specialized procedural knowledge and workflows to agents, enhancing their ability to perform complex tasks safely and effectively.

## Available Skills

### 1. Document Commands (`document-commands/`)
Enforces a **"Document-then-Execute"** workflow for all command-line operations.

- **Purpose**: Favor established project commands (e.g., `Makefile`, `npm run`) and log all executions for persistence and auditability.
- **Workflow**:
  1.  **Research**: Check for existing task runners.
  2.  **Creation**: Use a file-writing tool (e.g., `write_file`) to create a script in `.cmds-by-agents/` with `set -x`.
  3.  **Permissions**: Make the script executable (`chmod +x`).
  4.  **Execution**: Run the script directly.

## Usage
To use these skills globally, symlink them to your agent's skills directory:

```bash
mkdir -p ~/.agents/skills
ln -s "$(pwd)/document-commands" ~/.agents/skills/document-commands
```

Then, reload your agent session:
`/skills reload`

## Repository Structure
```
.
├── .cmds-by-agents/   # (Ignored) Local command logs
├── .gitignore         # Standard ignore patterns
├── SKILLS.md          # This index and documentation
└── README.md -> SKILLS.md # Symlink to this file
└── document-commands/ # Skill source
    ├── SKILL.md       # Skill manifest and instructions
    ├── assets/        # Reusable assets
    ├── references/    # Domain-specific references
    └── scripts/       # Helper scripts
```

---
*More skills coming soon...*
