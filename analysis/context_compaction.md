# Context Compaction Analysis

## Overview

Antigravity implements a multi-layered context compaction system to manage token limits while preserving important information across long conversations and multiple sessions.

## Compaction Mechanisms

### 1. Knowledge Items (KI) Distillation

The primary compaction mechanism - see `knowledge_items_system.md` for details.

- Conversations are distilled into structured Knowledge Items
- A separate KNOWLEDGE SUBAGENT handles the distillation
- KIs are searchable via semantic search

### 2. Checkpoint-Based Truncation

From extracted strings:

```
no checkpoint found, truncating conversation to step index %d.
current tokens: %d, token limit: %d

Checkpoint summary exceeded the max output token limit of %d
```

#### How It Works

1. **Checkpoint Creation**: System creates checkpoints at intervals
   - Controlled by `MaxStepsPerCheckpoint`
   - `GetCheckpointFrequency` determines frequency

2. **Token Monitoring**: System tracks token usage
   - `CachedContentTokenCount` - tracks cached tokens
   - `MaxContextTokens` / `MaxLocalContextTokens` - configurable limits

3. **Truncation**: When limits are exceeded
   - Truncates to most recent checkpoint
   - Checkpoint summaries can also be truncated if too long

### 3. Cascade Trajectory Summaries

Found types:
- `CascadeTrajectorySummary`
- `CascadeTrajectorySummaries`

These provide:
- Compact representations of conversation trajectories
- Provided at conversation start
- Contains what was discussed without full content

### 4. Code Item Tracking

From prompts:

```
The following is an important list of code items that you have previously
edited and viewed. Study this information carefully and only re-view code
items if it is absolutely required to proceed.
```

The system maintains a compact "working set" reference of:
- Files viewed
- Files edited
- Code snippets analyzed

### 5. Context Refresh System

#### Refresh Triggers

```
CONTEXT_REFRESH_REASON_CHAT - triggered by chat interactions
CONTEXT_REFRESH_REASON_IDE_ACTION - triggered by IDE actions (visible range changes, etc.)
```

#### Refresh Functions

- `RefreshContextForCacheWithDocument` - re-evaluates relevant context
- `FetchCachedContextAndRefreshDecision` - decides what to keep/discard
- `SetTrajectoryStartIndexForCachedContext` - marks where cached context begins

### 6. LRU Cache for Context

The system uses an LRU (Least Recently Used) cache:

```
lru.Cache[cciCacheKey, cciCacheValue]
```

Key components:
- `cciCacheKey` / `cciCacheValue` - Context Completion Index entries
- Automatic eviction of least relevant context when limits reached
- `SetPersistentCachedContext` - stores persistent context

### 7. Conversation Logs System

For historical context access:

```
Conversation logs are stored locally in the filesystem under:
{{SystemGeneratedLogsPath "agent-id"}}
```

#### When to Read Conversation Logs

1. New Conversation ID from @mention or reading another conversation
2. User explicitly mentions a specific conversation
3. User alludes to information that can't be identified from summaries

#### When NOT to Read

1. KI already covers the topic (search KIs first)
2. Summary shows conversation is not relevant
3. After reading overview, conclude it's not relevant

## Configuration Parameters

| Parameter | Purpose |
|-----------|---------|
| `MaxContextTokens` | Maximum tokens for context |
| `MaxLocalContextTokens` | Local context token limit |
| `CachedContentTokenCount` | Tracks cached content tokens |
| `MaxStepsPerCheckpoint` | Steps between checkpoints |
| `max_conversation_logs_chars` | Max chars for conversation logs |

## Context Window Metadata

The system tracks:

```
ContextWindowMetadata:
- MaxContextLength
- CachedContentTokenCount
- SlidingWindowAttn (sliding window attention support)
```

## Cumulative Context

For supported models:

- `CumulativeContextConfig` - configuration for cumulative context
- `GetSupportsCumulativeContext` - checks model support
- `SetTrajectoryStartIndexForCachedContext` - manages trajectory indices

## Template System

Prompts are managed via templates:

```
google3/third_party/jetski/prompt/template_provider/templates/
├── system_prompts/
│   ├── notify_user_tool.tmpl
│   ├── conversation_logs.tmpl
│   ├── ephemeral_message.tmpl
│   └── mode_descriptions.tmpl
└── helpers/
```

## Compaction Flow Summary

```
┌─────────────────────────────────────────────────────────────┐
│                    CONVERSATION START                        │
├─────────────────────────────────────────────────────────────┤
│  1. Load KI summaries (distilled knowledge)                 │
│  2. Load trajectory summaries (past conversation summaries) │
│  3. Load code item tracking (working set)                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    DURING CONVERSATION                       │
├─────────────────────────────────────────────────────────────┤
│  • Monitor token usage                                       │
│  • Create checkpoints at intervals                          │
│  • Refresh context on IDE actions                           │
│  • Use LRU cache for context items                          │
│  • Truncate to checkpoint when limits exceeded              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    CONVERSATION END                          │
├─────────────────────────────────────────────────────────────┤
│  • Knowledge Subagent distills conversation                 │
│  • Creates/updates Knowledge Items                          │
│  • Stores conversation logs                                 │
│  • Creates trajectory summary                               │
└─────────────────────────────────────────────────────────────┘
```

## Key Design Decisions

1. **Multi-layer approach**: Multiple mechanisms work together
2. **Semantic over chronological**: KIs use semantic search, not just recency
3. **Active maintenance**: System consolidates and prunes context
4. **Separation of concerns**: Distillation is a separate post-processing step
5. **Configurable limits**: Token limits are configurable per model/deployment

## Source

Extracted from: `language_server_windows_x64.exe`
Binary path: `resources/app/extensions/antigravity/bin/`
