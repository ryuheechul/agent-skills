# Agent Skills Repository

This repository provides specialized agent skills for various AI coding assistants and CLI agents.

## Project Goal
To provide specialized procedural knowledge and workflows to agents, enhancing their ability to perform complex tasks safely and effectively.

## Available Skills

### 1. Document Commands (`document-commands/`)
Enforces a **"Document-then-Execute"** workflow for all command-line operations.

- **Purpose**: Prioritize established project commands (e.g., `Makefile`, `npm run`) and log all executions for persistence and auditability.
- **Workflow**:
  1.  **Research**: Check for existing task runners or established scripts.
  2.  **Creation**: Use an agent's file-writing capability to create a script in `.cmds-by-agents/` with `set -x`.
  3.  **Permissions**: Ensure the script is executable (`chmod +x`).
  4.  **Execution**: Run the script directly.

## Installation

### Method 1: Using npx (Recommended)
You can install skills directly using the `npx skills` command:

```bash
npx skills add ryuheechul/agent-skills
```

### Method 2: Manual Setup (via Symlink)

#### 1. Clone the Repository
```bash
git clone https://github.com/ryuheechul/agent-skills.git
cd agent-skills
```

#### 2. Symlink to Agent Skills Directory
Link the desired skills to your agent's configuration directory. For example, in many environments:

```bash
mkdir -p ~/.agents/skills # or whatever directory that your harness loads skills from
ln -s "$(pwd)/document-commands" ~/.agents/skills/document-commands
```

### 3. Reload Agent Context
After installation, you must refresh your agent's session or reload its configuration to enable the new skills.
- **Gemini CLI**: Run `/skills reload`
- **Other Agents**: Restart the session or trigger a configuration refresh as required by your harness.

## Repository Structure
```
.
├── .cmds-by-agents/   # (Ignored) Local command logs
├── .gitignore         # Standard ignore patterns
├── SKILLS.md          # This index and documentation
├── README.md -> SKILLS.md # Symlink to this file
└── document-commands/ # Skill source
    ├── SKILL.md       # Skill manifest and instructions
    ├── assets/        # Reusable assets
    ├── references/    # Domain-specific references
    └── scripts/       # Helper scripts
```
