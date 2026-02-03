# Sample Agentic Workflow for Bob

Implementation of the Sample Agentic Workflow for Bob using markdown commands.

## Installation

Copy the commands to your Bob commands directory:

```bash
cp -r commands .bob/commands/
```

## Setup

1. Copy `AGENTS.md` to your project root
2. Customize it with your project-specific requirements
3. Keep it under 200 lines for optimal context management

## Available Commands

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
