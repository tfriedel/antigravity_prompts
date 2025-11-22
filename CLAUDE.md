# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Antigravity is a VS Code fork (based on VS Code 1.104.0) with integrated AI capabilities, developed by Google. It uses Open VSX as its extension marketplace and includes custom extensions for AI-powered coding assistance.

## Architecture

### Core Structure
- `resources/app/` - Main application code (compiled)
- `resources/app/extensions/` - Built-in and custom extensions
- `resources/app/out/` - Compiled VS Code core
- `resources/app/product.json` - Product configuration and branding

### Antigravity Extensions

Located in `resources/app/extensions/`:

| Extension | Purpose |
|-----------|---------|
| `antigravity` | Main AI extension powering chat, inline completions, terminal suggestions, and agent mode |
| `antigravity-browser-launcher` | Launches Antigravity-managed browser for web interactions |
| `antigravity-code-executor` | Executes AI-generated code from the cascade interface |
| `antigravity-dev-containers` | Dev container support (similar to VS Code Remote Containers) |
| `antigravity-remote-openssh` | SSH remote development |
| `antigravity-remote-wsl` | WSL remote development |

### Main Extension (`antigravity`)

Key features from package.json:
- **Authentication**: Custom `antigravity_auth` provider
- **Chat Interface**: `Ctrl+L` / `Cmd+L` opens chat with agent
- **Inline Commands**: `Ctrl+I` / `Cmd+I` for inline AI commands
- **Agent Mode**: `Ctrl+Shift+I` / `Cmd+Shift+I` triggers agent conversation
- **Commit Messages**: AI-generated commit messages via sparkle icon
- **Workflows**: Custom editors for `.agent/workflows/**/*.md` files
- **Rules**: Custom editors for `.agent/rules/**/*.md` files
- **MCP Support**: JSON schema validation for `mcp_config.json`

### External Dependencies

The main extension references external packages (from a monorepo structure):
- `@exa/typescript-utils` - TypeScript utilities
- `@exa/agent-ui-toolkit` - Agent UI components
- `@exa/chat_client` - Chat client implementation
- `@exa/design_system` - Design system components

### Tech Stack (antigravity extension)

- **Frontend**: React with TypeScript
- **Styling**: Tailwind CSS with custom brand colors
- **Build**: Webpack with Babel (TypeScript + React presets)
- **Linting**: ESLint with strict TypeScript checking
- **Formatting**: Prettier with import sorting

### Brand Colors

Primary colors defined in `tailwind.config.js`:
- `brand-dark`: `#09b6a2` (teal)
- `brand-light`: `#71E9D8` (light teal)

## Key Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl/Cmd+L` | Open Chat with Agent |
| `Ctrl/Cmd+I` | Inline AI command (editor) / Terminal command (terminal) |
| `Ctrl/Cmd+Shift+I` | Start Agent Conversation |
| `Ctrl/Cmd+Shift+L` | Open new chat conversation |
| `Alt+Enter` | Accept agent step / Accept focused hunk |
| `Alt+J/K` | Navigate between agent edit hunks |
| `Tab` | Accept inline completion |

## Configuration

Key settings in `antigravity.` namespace:
- `marketplaceExtensionGalleryServiceURL` - Extension marketplace URL (default: Open VSX)
- `searchMaxWorkspaceFileCount` - Max files for workspace embeddings (default: 5000)

Remote server downloads from:
```
https://antigravity-stable.codeiumdata.com/${os}-reh-${arch}/${quality}/${commit}/antigravity-reh-${os}-${arch}-${ideVersion}.tar.gz
```

## Cortex Agent System (Implementation Plans)

The AI agent uses the **Cortex** system for planning and execution. Plans are generated server-side and displayed locally.

### Plan State (`CortexPlanState`)

Tracks plan lifecycle:
- `planId` - Unique identifier
- `planFullyGenerated` - Generation complete
- `planFinished` - Execution complete
- `planSummaryConfirmed` - User confirmed
- `planSummaryFullyGenerated` - Summary ready

### Brain System (Task Tracking)

Plans use a hierarchical "Brain" for task management:

- `BrainEntry` - Task item with `id`, `content`, `status`, `parentId`, `prevSiblingId`
- `BrainEntryDelta` - Tracks task changes
- `PlanEntryDeltaSummary` - Items added/completed
- `BrainUpdateStrategy` - Controls update behavior

### Cortex Step Types

Key steps in the planning/execution flow:

| Step Type | Purpose |
|-----------|---------|
| `CortexStepUserInput` | User's request |
| `CortexStepPlannerResponse` | AI's natural language plan |
| `CortexStepFileBreakdown` | Files to modify with paths |
| `CortexStepBrainUpdate` | Creates/updates task items |
| `CortexStepProposeCode` | Proposes code changes |
| `CortexStepCodeAction` | Executes code modifications |
| `CortexStepRunCommand` | Runs terminal commands |
| `CortexStepCheckpoint` | Saves plan state |
| `CortexStepTaskBoundary` | Marks task boundaries |
| `CortexStepFinish` | Completes execution |

### Agent Modes

- `READ-ONLY mode` - Edits via `propose_code` tool
- `EDIT mode` - Edits via direct edit tool

### Planner Configuration

```javascript
CascadePlannerConfig {
  plannerTypeConfig: {
    case: "conversational",
    value: CascadeConversationalPlannerConfig
  }
}
```

### Plan Generation Flow

1. User input → `CortexStepUserInput`
2. AI planning → `CortexStepPlannerResponse` (natural language)
3. File analysis → `CortexStepFileBreakdown`
4. Task creation → `CortexStepBrainUpdate`
5. Execution → Various action steps
6. Completion → `CortexStepFinish`

## Prompt Storage

Most AI prompts are server-side. Local prompts found in `extension.js`:
- Mode prompts: "You are in READ-ONLY mode..." / "You are in EDIT mode..."
- Context prompts: "The IDE found these problems in the code..."

Instruction builders (compiled):
- `createEditIntentInstructions()`
- `createStrictEditInstructions()`
- `createRefreshContextInstructions()`
- `createIntentUnderstandingPrompt()`

## Documentation Links

- Docs: https://antigravity.google/docs
- Rules: https://antigravity.google/docs/rules
- MCP: https://antigravity.google/docs/mcp
- Support: https://antigravity.google/support
- Changelog: https://antigravity.google/changelog

## Notes

- This is an installed binary distribution, not the source repository
- Source files (`.ts`, `.tsx`) are compiled to `dist/` and `out/` directories
- The CLI alias is `agy`
- User data stored in `.antigravity` folder
- URL protocol handler: `antigravity://`
