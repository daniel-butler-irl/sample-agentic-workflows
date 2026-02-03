# Sample Agentic Workflow for Claude Code

Implementation of the Sample Agentic Workflow for Claude Code using skills.

## Installation

Choose one of the following installation options:

### Option 1: Project-Specific Skills (Recommended)

Install skills for this project only:

```bash
# From the repository root
cp -r claude-code/skills .claude/skills/
```

### Option 2: Personal Skills

Install skills for all your Claude Code projects:

```bash
# From the repository root
cp -r claude-code/skills/* ~/.claude/skills/
```

## Setup

1. Copy `CLAUDE.md` to your project root as `.claude/CLAUDE.md` or `AGENTS.md`
2. Customize it with your project-specific requirements
3. Keep it under 200 lines for optimal context management

## Available Skills

### On-Demand Phases
- `/investigate` - Understand code or enrich sparse issues
- `/design` - Define interfaces (signatures only, no implementation)

### Core Workflow
- `/01-issue-plan` - Define 1-3 issues with success criteria
- `/02-task-plan` - Plan tasks based on gates and complexity
- `/03-implement` - Execute single task with verification
- `/04-cleanup` - Three-phase audit before PR

### Meta Phases
- `/summarise` - Checkpoint session for handoff
- `/workflow` - Explain methodology and answer questions

## Usage

Start a new session for each task to prevent context pollution. The workflow enforces small, reviewable commits and explicit verification gates.

See [docs/methodology.md](../docs/methodology.md) for complete workflow documentation.
