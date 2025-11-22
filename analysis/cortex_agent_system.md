# Cortex Agent System Analysis

## Overview

The Cortex system is Antigravity's core agent architecture for planning and executing coding tasks. It uses a step-based execution model with various step types for different operations.

## Architecture

### Core Components

1. **Cortex Steps** - Individual units of execution
2. **Trajectory** - Sequence of steps forming a conversation
3. **Brain System** - Hierarchical task tracking
4. **Planner** - Generates execution plans

## Cortex Step Types

From extracted binary strings, the following step types were identified:

### Planning Steps

| Step Type | Purpose |
|-----------|---------|
| `CortexStepUserInput` | User's request/input |
| `CortexStepPlannerResponse` | AI's natural language plan |
| `CortexStepFileBreakdown` | Files to modify with paths |
| `CortexStepBrainUpdate` | Creates/updates task items |

### Execution Steps

| Step Type | Purpose |
|-----------|---------|
| `CortexStepProposeCode` | Proposes code changes (READ-ONLY mode) |
| `CortexStepCodeAction` | Executes code modifications |
| `CortexStepRunCommand` | Runs terminal commands |
| `CortexStepViewCodeItem` | Views code/files |
| `CortexStepGrepSearch` | Searches codebase |

### Control Steps

| Step Type | Purpose |
|-----------|---------|
| `CortexStepCheckpoint` | Saves plan state |
| `CortexStepTaskBoundary` | Marks task boundaries |
| `CortexStepFinish` | Completes execution |

### Knowledge Steps

| Step Type | Purpose |
|-----------|---------|
| `CortexStepReadKnowledgeBaseItem` | Reads a Knowledge Item |
| `CortexStepReadUrlContent` | Reads URL content |

## Brain System (Task Tracking)

The Brain system provides hierarchical task management:

### BrainEntry

```
BrainEntry:
- id: unique identifier
- content: task description
- status: pending/in_progress/completed
- parentId: parent task ID
- prevSiblingId: ordering
```

### Related Types

- `BrainEntryDelta` - Tracks task changes
- `PlanEntryDeltaSummary` - Items added/completed
- `BrainUpdateStrategy` - Controls update behavior

## Agent Modes

### READ-ONLY Mode

```
"You are in READ-ONLY mode..."
```

- Edits via `propose_code` tool
- User must approve changes

### EDIT Mode

```
"You are in EDIT mode..."
```

- Edits via direct edit tool
- More autonomous operation

## Plan Generation Flow

```
1. User input → CortexStepUserInput
2. AI planning → CortexStepPlannerResponse (natural language)
3. File analysis → CortexStepFileBreakdown
4. Task creation → CortexStepBrainUpdate
5. Execution → Various action steps
6. Checkpointing → CortexStepCheckpoint (periodic)
7. Completion → CortexStepFinish
```

## Planner Configuration

```javascript
CascadePlannerConfig {
  plannerTypeConfig: {
    case: "conversational",
    value: CascadeConversationalPlannerConfig
  }
}
```

## Step Status Types

```
CORTEX_STEP_STATUS_UNSPECIFIED
CORTEX_STEP_STATUS_PENDING
CORTEX_STEP_STATUS_IN_PROGRESS
CORTEX_STEP_STATUS_COMPLETED
CORTEX_STEP_STATUS_FAILED
```

## Trajectory Management

### CortexPlanState

Tracks plan lifecycle:

| Field | Purpose |
|-------|---------|
| `planId` | Unique identifier |
| `planFullyGenerated` | Generation complete |
| `planFinished` | Execution complete |
| `planSummaryConfirmed` | User confirmed |
| `planSummaryFullyGenerated` | Summary ready |

### Trajectory Summaries

- `CascadeTrajectorySummary` - Summary of a trajectory
- `CascadeTrajectorySummaries` - Collection of summaries

## gRPC Services

Key services for Cortex operations:

```
/exa.api_server_pb.ApiServerService/RecordCortexStep
/exa.api_server_pb.ApiServerService/RecordCortexTrajectoryStep
/exa.api_server_pb.ApiServerService/ApplyTrajectoryHeuristics
/exa.api_server_pb.ApiServerService/RecordCortexGeneratorMetadata
/exa.api_server_pb.ApiServerService/RecordCortexExecutionMetadata
/exa.language_server_pb.LanguageServerService/GetCascadeTrajectory
/exa.language_server_pb.LanguageServerService/DeleteCascadeTrajectory
/exa.language_server_pb.LanguageServerService/RevertToCascadeStep
```

## Checkpointing

Checkpoints are created:
- At regular intervals (`MaxStepsPerCheckpoint`)
- Before major operations
- For rollback capability

From prompts:
```
Checkpoint summary exceeded the max output token limit of %d
no checkpoint found, truncating conversation to step index %d
```

## Tool Integration

Steps integrate with various tools:

### Code Tools
- `propose_code` - Propose code changes
- `edit_file` / `replace_file_content` - Direct edits
- `view_file` - View files

### Search Tools
- `grep_search` - Search codebase
- `list_dir` - List directories
- `semantic_search` - Semantic code search

### Execution Tools
- `run_command` - Run terminal commands
- `browser_subagent` - Browser automation

### Knowledge Tools
- `read_knowledge_base_item` - Read KIs
- `write_to_file` - Create artifacts

## Error Handling

Step execution includes:
- Status tracking (success/failure)
- Error trace metadata
- Feedback recording

```
/exa.api_server_pb.ApiServerService/RecordCortexCodingStepFeedback
```

## Source

Extracted from: `language_server_windows_x64.exe`
Binary path: `resources/app/extensions/antigravity/bin/`
