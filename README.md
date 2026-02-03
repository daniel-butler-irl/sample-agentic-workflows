# Sample Agentic Workflows

A structured methodology for working with AI coding agents in enterprise environments.

## Overview

This repository provides workflow implementations for AI-assisted development. The methodology addresses common failure modes when working with AI agents and provides a systematic approach to planning, implementing, and reviewing AI-generated code.

For detailed background on the failure modes and design principles behind these workflows, see: [Designing Agentic Workflows: Where Agents Fail, and Where We Fail](https://dev.to/danielbutlerirl/designing-agentic-workflows-where-agents-fail-and-where-we-fail-4a95)

## Purpose

These workflows are designed for developers who:

- Are new to agentic coding and want a cautious, structured approach
- Need reviewable commits and clear verification gates
- Work in enterprise environments requiring accountability
- Want to prevent common AI failure modes (premature completion claims, test deletion, hardcoded solutions)

As AI coding tools improve and you gain experience, you should streamline these workflows and design approaches that work best for your team and projects. This is a starting point, not a permanent prescription.

## Understanding Failure Modes

Working with AI agents requires awareness of common failure patterns:

### Agent Failure Modes

- **Baby-Counting**: Silently drops requirements or deletes tests to appear complete
- **Cardboard Muffin**: Delivers hollow implementations (hardcoded values, stubbed logic)
- **Half-Assing**: Technically functional but poorly architected code
- **Litterbug**: Leaves debug code, temporary comments, and dead code behind

### Human Failure Modes

- **Rubber-Stamping**: Approving without reviewing due to review fatigue
- **Intent Drift**: Original goals lost as conversation lengthens
- **Decision Delegation**: Letting AI make architectural choices without explicit evaluation
- **Review Fatigue**: Overwhelmed by large diffs or comprehensive specifications

The workflows in this repository provide structure and checkpoints to detect and prevent these patterns.

## Available Implementations

| Tool | Directory | Format | Setup |
|------|-----------|--------|-------|
| **Claude Code** | [claude-code/](claude-code/) | SKILL.md in directories | Copy to `.claude/skills/` |
| **Codex CLI** | [codex-cli/](codex-cli/) | SKILL.md in directories | Copy to `~/.codex/skills/` |
| **Gemini CLI** | [gemini-cli/](gemini-cli/) | TOML commands | Copy to `~/.gemini/commands/` |
| **Bob** | [bob/](bob/) | Markdown commands | Copy to `.bob/commands/` |

Each implementation includes tool-specific setup instructions and an AGENTS file template for project-level requirements.

## Workflow Overview

### On-Demand Phases (use when needed)

- **Investigate**: Understand existing code or enrich sparse issues
- **Design**: Define interfaces and architecture (signatures only, no implementation)

### Core Workflow

1. **Issue Planning**: Define 1-3 issues with clear success criteria
2. **Task Planning**: Break issues into commit-sized tasks (50-200 lines each)
3. **Implementation**: Execute single task with explicit verification
4. **Cleanup**: Three-phase audit before PR (audit, fix, validate)

### Key Principles

- **Fresh sessions per task**: Prevents context pollution
- **Hard verification gates**: Clear pass/fail criteria, not subjective review
- **One task, one commit**: Reviewable units of work
- **Human approval required**: For scope changes, dependencies, and commits
- **Test protection**: Never modify tests to make them pass

## Documentation

- **[Methodology Guide](docs/methodology.md)**: Complete workflow explanation with rationale
- **Tool-specific READMEs**: Setup and usage instructions for each implementation

## Getting Started

1. Choose your AI coding tool from the implementations above
2. Follow the setup instructions in that tool's directory
3. Read the methodology guide to understand the workflow phases
4. Start with Issue Planning for your first task

## License

Apache 2.0 - See [LICENSE](LICENSE) for details

## Contributing

This is a reference implementation. Fork and adapt for your team's needs.
