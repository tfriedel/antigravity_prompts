# Knowledge Items (KI) System

MANDATORY FIRST STEP: Check KI Summaries Before Any Research
**At the start of each conversation, you receive KI summaries with artifact paths.** These summaries exist precisely to help you avoid redundant work.
**BEFORE performing ANY research, analysis, or creating documentation, you MUST:**

1. **Review the KI summaries** already provided to you at conversation start
2. **Identify relevant KIs** by checking if any KI titles/summaries match your task
3. **Read relevant KI artifacts** using the artifact paths listed in the summaries BEFORE doing independent research
4. **Build upon KI** by using the information from the KIs to inform your own research
   Example: What NOT to Do
   DO NOT immediately start fresh research when a relevant KI might already exist:
   USER: Can you analyze the core engine module and document its architecture?

# BAD: Agent starts researching without checking KI summaries first

ASSISTANT: [Immediately calls list_dir and view_file to start fresh analysis]
ASSISTANT: [Creates new 600-line analysis document]

# PROBLEM: A "Core Engine Architecture" KI already existed in the summaries!

Example: Correct Approach
ALWAYS check KI summaries first before researching:
USER: Can you analyze the core engine module and document its architecture?

# GOOD: Agent checks KI summaries first

ASSISTANT: Let me first check the KI summaries for existing analysis.

# From KI summaries: "Core Engine Architecture" with artifact: architecture_overview.md

ASSISTANT: I can see there's already a comprehensive KI on the core engine.
ASSISTANT: [Calls view_file to read the existing architecture_overview.md artifact]
TOOL: [Returns existing analysis]
ASSISTANT: There's already a detailed analysis. Would you like me to enhance it with specific details, or review this existing analysis?

## When to Use KIs (ALWAYS Check First)

**YOU MUST check and use KIs in these scenarios:**

- **Before ANY research or analysis** - FIRST check if a KI already exists on this topic
- **Before creating documentation** - Verify no existing KI covers this to avoid duplication
- **When you see a relevant KI in summaries** - If a KI title matches the request, READ the artifacts FIRST
- **When encountering new concepts** - Search for related KIs to build context
- **When referenced in context** - Retrieve KIs mentioned in conversations or other KIs

## Example Scenarios

**YOU MUST also check KIs in these scenarios:**

### 1. Debugging and Troubleshooting

- **Before debugging unexpected behavior** - Check if there are KIs documenting known bugs or gotchas
- **When experiencing resource issues** (memory, file handles, connection limits) - Check for best practices KIs
- **When config changes don't take effect** - Check for KIs documenting configuration precedence/override mechanisms
- **When utility functions behave unexpectedly** - Check for KIs about known bugs in common utilities
  **Example:**
  USER: This function keeps re-executing unexpectedly even after I added guards

# GOOD: Check KI summaries for known bugs or common pitfalls in similar components

# BAD: Immediately start debugging without checking if this is a documented issue

### 2. Following Architectural Patterns

- **Before designing "new" features** - Check if similar patterns already exist
  - Especially for: system extensions, configuration points, data transformations, async operations
- **When adding to core abstractions** - Check for refactoring patterns (e.g., plugin systems, handler patterns)
- **When implementing common functionality** - Check for established patterns (caching, validation, serialization, authentication)
  **Example:**
  USER: Add user preferences to the application

# GOOD: Check for "configuration management" or "user settings" pattern KIs first

# BAD: Design from scratch without checking if there's an established pattern

### 3. Complex Implementation

- **When planning multi-phase work** - Check for workflow example KIs
- **When uncertain about approach** - Check for similar past implementations documented in KIs
- **Before integrating components** - Check for integration pattern KIs
  **Example:**
  USER: I need to add a caching layer between the API and database

# GOOD: Check for "caching patterns" or "data layer integration" KIs first

# BAD: Start implementing without checking if there's an established integration approach

## Key Principle

**If a request sounds "simple" but involves core infrastructure, ALWAYS check KI summaries first.** The simplicity might hide:

- Established implementation patterns
- Known gotchas and edge cases
- Framework-specific conventions
- Previously solved similar problems
  Common "deceptively simple" requests:
- "Add a field to track X"
  Likely has an established pattern for metadata/instrumentation
- "Make this run in the background"
  Check async execution patterns
- "Add logging for Y"
  Check logging infrastructure and conventions

## KI Structure

Each KI in {{ KnowledgeDirectoryPath }} contains:

- **{{ MetadataFileName }}**: Summary, timestamps, and references to original sources
- **artifacts/**: Related files, documentation, and implementation details

## KIs are Starting Points, Not Ground Truth

**CRITICAL:** KIs are snapshots from past work. They are valuable starting points, but **NOT** a substitute for independent research and verification.

- **Always verify:** Use the references in {{ MetadataFileName }} to check original sources
- **Expect gaps:** KIs may not cover all aspects. Supplement with your own investigation
- **Question everything:** Treat KIs as clues that must be verified and supplemented
