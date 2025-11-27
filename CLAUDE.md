# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository contains extracted system prompts and documentation from Antigravity (Google's VS Code fork with AI capabilities, based on VS Code 1.104.0). It's a research/documentation archive, not an executable codebase.

## Repository Structure

```
antigravity_prompts/
├── prompts/                    # Extracted AI prompts
│   ├── planning-mode.txt       # Full agentic system prompt (~600 lines)
│   ├── Fast Prompt.txt         # Simplified system prompt (~300 lines)
│   ├── modes.md                # PLANNING/EXECUTION/VERIFICATION mode definitions
│   ├── knowledge_items.md      # Knowledge Items (KI) persistent memory system
│   ├── task.md                 # Checklist-based task tracking format
│   ├── implementation_plan.md  # Implementation plan artifact template
│   └── walkthrough.md          # Post-implementation summary template
├── analysis/                   # Technical analysis documents
│   ├── cortex_agent_system.md  # Cortex planning/execution system
│   ├── knowledge_items_system.md
│   └── context_compaction.md
├── REVERSE_ENGINEERING.md      # Extraction methodology and techniques
└── README.md                   # Project overview
```

## Key Prompts

### `planning-mode.txt` - Main System Prompt
Complete agentic prompt containing:
- Agent identity ("You are Antigravity, a powerful agentic AI coding assistant designed by the Google Deepmind team")
- Three modes: PLANNING, EXECUTION, VERIFICATION
- 25+ tool definitions (codebase_search, run_command, browser_subagent, etc.)
- Task boundary system for complex multi-step work
- Artifact system (task.md, implementation_plan.md, walkthrough.md)

### `Fast Prompt.txt` - Simplified Prompt
Shorter version missing full agentic features like task boundaries and persistent context.

## Agent Modes

| Mode | Purpose |
|------|---------|
| PLANNING | Research, create implementation_plan.md, get user approval |
| EXECUTION | Implement the approved plan |
| VERIFICATION | Prove work is complete, create walkthrough.md with evidence |

## Extraction Techniques

See `REVERSE_ENGINEERING.md` for details. Primary methods:
- `strings` extraction from `language_server_windows_x64.exe` binary
- Analysis of `extension.js` for protobuf definitions
- DevTools inspection at runtime

## Source Application Notes

Antigravity uses the **Cortex** system for AI planning. Key components:
- **CortexStep*** types define the planning/execution flow
- **BrainEntry** for hierarchical task tracking
- Protobuf communication with `antigravity-stable.codeiumdata.com`
- Tool calls required in responses: "You are REQUIRED to call a tool in your response."
