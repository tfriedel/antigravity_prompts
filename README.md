# Antigravity Prompts

Extracted system prompts from Antigravity (Google's VS Code fork with AI capabilities).

## Overview

This repository contains system prompts and instructions that power Antigravity's AI agent. These prompts define:

- **Agent Identity & Behavior**: How the AI presents itself and interacts
- **Workflow Modes**: PLANNING, EXECUTION, and VERIFICATION phases
- **Knowledge Management**: Persistent memory via Knowledge Items (KI)
- **Task Tracking**: Structured task management with artifacts
- **Tool Usage**: Available tools and how to use them

## Directory Structure

```
antigravity_prompts/
├── prompts/
│   ├── planning-mode.txt      # Full system prompt (agentic mode)
│   ├── Fast Prompt.txt        # Simplified system prompt
│   ├── modes.md               # PLANNING/EXECUTION/VERIFICATION modes
│   ├── knowledge_items.md     # Knowledge Items (KI) system
│   ├── task.md                # Task management with checklists
│   ├── implementation_plan.md # Implementation plan artifact format
│   └── walkthrough.md         # Walkthrough artifact format
├── CLAUDE.md                  # Project documentation
├── REVERSE_ENGINEERING.md     # Extraction methodology
└── README.md
```

## Prompt Files

### `planning-mode.txt` - Main System Prompt

The complete agentic system prompt (~600 lines) containing:

- **Identity**: "You are Antigravity, a powerful agentic AI coding assistant designed by the Google Deepmind team"
- **User Information**: Workspace paths, OS details, access restrictions
- **Agentic Mode**: Task boundary system for complex multi-step work
- **Mode Descriptions**: PLANNING, EXECUTION, VERIFICATION phases
- **Artifact System**: task.md, implementation_plan.md, walkthrough.md
- **Tool Definitions**: 25+ tools including browser_subagent, codebase_search, run_command, etc.
- **Web Development Guidelines**: Tech stack, design aesthetics, SEO best practices
- **Workflows**: Custom workflow system via `.agent/workflows/*.md`
- **Communication Style**: GitHub markdown formatting, proactiveness guidelines

### `Fast Prompt.txt` - Simplified Prompt

A shorter version (~300 lines) with essential identity, workspace info, tools, and web development guidelines. Missing the full agentic mode features like task boundaries and persistent context.

### `modes.md` - Agent Modes

Defines the three operational modes:

| Mode | Purpose |
|------|---------|
| **PLANNING** | Research, understand requirements, create implementation_plan.md, get user approval |
| **EXECUTION** | Implement the approved plan, return to PLANNING if encountering issues |
| **VERIFICATION** | Prove work is complete, capture evidence, create walkthrough.md |

### `knowledge_items.md` - Knowledge Items System

Instructions for using the persistent memory system:

- **Mandatory First Step**: Check KI summaries before ANY research
- **KI Structure**: Each KI has `metadata.json` and `artifacts/` directory
- **When to Use**: Debugging, following patterns, complex implementations
- **Key Principle**: KIs are starting points, not ground truth - always verify

### `task.md` - Task Management

Checklist-based task tracking:
```markdown
- [ ] uncompleted task    <!-- id: 0 -->
- [/] in progress task    <!-- id: 1 -->
- [x] completed task      <!-- id: 2 -->
```

### `implementation_plan.md` - Plan Artifact

Template for technical plans with:
- Goal description and context
- User review requirements
- Proposed changes grouped by component
- Verification plan (automated tests + manual verification)
- Confidence assessment (6-question framework)

### `walkthrough.md` - Completion Artifact

Post-implementation summary with:
- Changes made
- Verification results
- Embedded screenshots/recordings

## Key Features

### Task Boundary System

For complex tasks, the agent uses `task_boundary` tool to:
- Set TaskName, TaskSummary, TaskStatus
- Track mode (PLANNING/EXECUTION/VERIFICATION)
- Update progress without overwhelming the user

### Confidence Assessment

Before notifying users, agents must rate confidence (0.0-1.0) based on:
1. Gaps in addressing the request?
2. Unverified assumptions?
3. Complex logic with unknowns?
4. Non-trivial interaction risks?
5. Ambiguous requirements?
6. Irreversible changes?

### Persistent Context

Two mechanisms for cross-conversation memory:
1. **Conversation Logs**: Raw history in `.system_generated/logs`
2. **Knowledge Items**: Distilled knowledge by KNOWLEDGE SUBAGENT

### Available Tools

| Category | Tools |
|----------|-------|
| **Search** | codebase_search, grep_search, find_by_name, search_in_file, search_web |
| **Files** | view_file, write_to_file, replace_file_content, multi_replace_file_content |
| **Terminal** | run_command, command_status, send_command_input, read_terminal |
| **Browser** | browser_subagent, read_url_content |
| **Analysis** | view_file_outline, view_code_item, list_dir |
| **Media** | generate_image |

## Source

Extracted from Antigravity 1.104.0 installation.

## Disclaimer

This is a reverse-engineering effort for educational purposes. The extracted content may be incomplete or partially accurate.
