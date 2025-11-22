# Reverse Engineering Antigravity Prompts

This document describes techniques for extracting and analyzing the AI prompts used by Antigravity IDE.

## Overview

Antigravity is a VS Code fork with AI capabilities. The AI system (called "Cortex") uses:
- A local **Language Server binary** (Go-based) that contains embedded prompts
- A remote **backend server** at `antigravity-stable.codeiumdata.com`
- **Protocol Buffers (protobuf)** for client-server communication

## Key Files

| File | Purpose |
|------|---------|
| `resources/app/extensions/antigravity/bin/language_server_windows_x64.exe` | Language Server binary with embedded prompts |
| `resources/app/extensions/antigravity/dist/extension.js` | VS Code extension (minified JS) |
| `%APPDATA%/Antigravity/logs/*/window1/exthost/google.antigravity/Antigravity.log` | Runtime logs |

## Technique 1: Extract Strings from Language Server Binary

The simplest and most effective method. The Language Server binary contains all system prompts as embedded strings.

### Basic Command

```bash
strings /path/to/language_server_windows_x64.exe | grep -iE "You are|system.*prompt"
```

### Extract All Prompts

```bash
# Identity prompts
strings language_server_windows_x64.exe | grep -iE "^You are (a |an |Antigravity|Cascade|Jetski)"

# Mode instructions (PLANNING, EXECUTION, VERIFICATION)
strings language_server_windows_x64.exe | grep -iE "PLANNING mode|EXECUTION mode|VERIFICATION mode|AGENTIC mode"

# Behavioral rules
strings language_server_windows_x64.exe | grep -iE "^(You must|You should|You are REQUIRED|IMPORTANT:|Do not)"

# Tool instructions
strings language_server_windows_x64.exe | grep -iE "tool.*call|call.*tool"
```

### Comprehensive Dump

```bash
strings language_server_windows_x64.exe | grep -E "^(You are|You should|You must|You may|IMPORTANT:|Description:|When you|If you|Do not|Always |Never )" | sort | uniq > prompts.txt
```

## Technique 2: Analyze Extension JavaScript

The `extension.js` file contains protobuf definitions and some UI-related prompts.

### Find Protobuf Message Types

```bash
grep -oP 'typeName="exa\.[^"]+' extension.js | sort | uniq
```

### Key Proto Types

- `exa.cortex_pb.CortexStepUserInput` - User messages
- `exa.cortex_pb.CortexStepPlannerResponse` - AI plan responses
- `exa.cortex_pb.ChatModelMetadata` - Contains `system_prompt` field
- `exa.cortex_pb.BrainEntry` - Task/plan items

### Extract Prompt-Related Code

```bash
grep -oP 'createEditIntentInstructions|createStrictEditInstructions|createRefreshContextInstructions|createIntentUnderstandingPrompt' extension.js
```

## Technique 3: DevTools Inspection

1. Open Antigravity
2. Press `Ctrl+Shift+I` to open DevTools
3. Go to **Sources** tab
4. Press `Ctrl+Shift+F` to search all files
5. Search for `system_prompt` or `systemPrompt`
6. Set breakpoints to inspect values at runtime

### Network Tab

1. Open DevTools → Network tab
2. Filter by `codeiumdata.com`
3. Requests are protobuf-encoded but headers are readable

## Technique 4: Process & Port Discovery

Based on [Alok Bishoyi's blog post](https://alokbishoyi.com/blogposts/reverse-engineering-browser-automation.html):

```bash
# Find Chrome remote debugging port
ps aux | grep Chrome
lsof -i :9222

# Scan for extension HTTP servers
for port in {3025..3035}; do
  curl -s "http://127.0.0.1:$port/.identity" && echo " -> Port $port"
done
```

## Key Findings

### Identity Prompts Found

```
You are Antigravity Agent, a powerful agentic AI coding assistant designed by the Google engineering team.

You are Antigravity, a powerful agentic AI coding assistant designed by the Google Deepmind team working on Advanced Agentic Coding.

You are a subagent of Jetski, a powerful agentic AI coding assistant designed by the Google Deepmind team working on Advanced Agentic Coding.

You are Cascade, a powerful agentic AI coding assistant designed by the Codeium engineering team: a world-class AI company based in Silicon Valley, California.
```

### Mode Descriptions

**PLANNING mode:**
> Perform deep research independently, iterate with implementation_plan.md, validate intuitions, resolve uncertainties, research verification strategies.

**EXECUTION mode:**
> Execute the implementation plan independently. If errors or unexpected results occur, transition back to PLANNING mode.

**VERIFICATION mode:**
> Prove work is correct with comprehensive methodology. Create verification artifacts, show methodology, use browser tools for web apps.

**AGENTIC mode:**
> Take more time to research and think deeply. Interact less often, do as much work independently as possible, verify before presenting.

### Behavioral Rules

- `You are REQUIRED to call a tool in your response.`
- `You are not allowed to access files not in active workspaces.`
- `Do not update the status too frequently, leave at minimum two tool calls in between status updates.`
- `IMPORTANT: The TargetContent of each ReplacementChunk MUST be a unique substring within the file.`

## Architecture Notes

```
┌─────────────────────────────────────────────────────────────┐
│                     Antigravity IDE                         │
├─────────────────────────────────────────────────────────────┤
│  extension.js                                               │
│  ├── Protobuf message classes (CortexStep*, BrainEntry)    │
│  ├── UI prompt snippets (mode switching, IDE context)       │
│  └── gRPC-web client                                        │
├─────────────────────────────────────────────────────────────┤
│  language_server_windows_x64.exe                            │
│  ├── Full system prompts (embedded strings)                 │
│  ├── Tool definitions                                       │
│  ├── Mode instructions                                      │
│  └── Behavioral rules                                       │
├─────────────────────────────────────────────────────────────┤
│                          ↓ gRPC/protobuf                    │
│           antigravity-stable.codeiumdata.com                │
│  └── AI inference, context management, response generation  │
└─────────────────────────────────────────────────────────────┘
```

## Tools Used

- `strings` - Extract readable strings from binaries
- `grep` - Pattern matching
- DevTools - Runtime inspection
- `lsof` / `ps` - Process discovery

## References

- [Reverse Engineering Browser Automation](https://alokbishoyi.com/blogposts/reverse-engineering-browser-automation.html)
- Antigravity Docs: https://antigravity.google/docs
