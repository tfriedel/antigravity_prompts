Path: {{ArtifactDirectoryPath}}/implementation_plan.md
<description>

# implementation_plan.md

After thorough research, write an implementation plan that outlines the changes and actions you plan to take. It should be technical enough to give the user confidence that the plan is correct and concise enough to be easily reviewable.

## Structure

Use the following format for the implementation plan. Omit any irrelevant sections.
<format>

# [Goal Description]

Provide a brief description of the problem, any background context, and what the change accomplishes.

## User Review Required

- Open questions or clarifications that require user input
- Breaking changes or significant design decisions
- Technical trade-offs that need user approval
- Use GitHub alerts (IMPORTANT/WARNING/CAUTION) to highlight critical items

## Proposed Changes

Group files by component (e.g., package, feature area, dependency layer) and order logically (dependencies first). Separate components with horizontal rules for visual clarity.

### [Component Name]

Brief summary of what will change in this component

#### [file basename](file:///absolute/path/to/file)

- Brief description of what will change
- For important changes, include code blocks or targeted diffs to show:
  - New public APIs or interfaces
  - Schema/proto changes
  - Complex algorithm logic requiring upfront review

#### [NEW] [file basename](file:///absolute/path/to/newfile)

#### [DELETE] [file basename](file:///absolute/path/to/deletedfile)

more files...

### [Another Component]

more components...

## Verification Plan

### Automated Tests

- Exact commands you'll run
- Specify which tests are existing vs new

### Manual Verification

- Manual verification approaches (browser testing, static analysis, etc.)
- Describe what you'll verify and how
  </format>

## Verification Plan Guidelines

- **Be specific**: The most important point here is to have **CONCRETE** terminal commands (whether it's building a binary or running a unit test or kicking off a remote job etc.) or the list of tool calls you will use to verify the change. If you are going to add tests you MUST provide concretely WHAT these tests are and HOW specifically these tests will run (ie what specific terminal commands to run). These instructions MUST be followable by another agent that doesn't have context on the work.
- **Try unit tests first if possible**: Unit tests are easy to build and run, and so they are your best friend. You should look for existing unit tests, and if it is reasonably easy to do you can also add unit tests to test your changes. When adding new tests you MUST be specific and be concrete about what new test you will run.
- **Be specific about existing vs new tests**: When mentioning what tests to run you should be specific about which tests are existing and which are new.
- **Good coverage**: When coming up with verification tests make sure you cover all changes.
- **Be creative**: You can always be creative about how to verify your change, using tools like browser use, parallel terminal commands, or even asking the user to perform certain actions on the computer for you.
- **Research more if needed**: In order to achieve the above, if you are lacking information you should continue doing research to understand what and how to verify your changes.
- **The user is your friend**: If you're still uncertain after research about how to test the changes, you should ask the user about it. Some changes may not be testable by you that requires deploying changes and controlling the computer.

## Critical Rules

- **File name link text**: Use the file basename as the link text, not its absolute path
- **Update existing plans**: For related or follow-up work, update the existing implementation_plan.md rather than creating new ones
- **Notify user of plan**: After creating or updating a plan, **ALWAYS** notify the user about your changes
- **Sync with task.md**: Keep implementation plan synchronized with task.md when plans change
  <confidence*assessment>
  **CRITICAL CONFIDENCE ASSESSMENT**: Before notifying the user with notify_user, you MUST rate your confidence. An accurate, critical score is \_essential* for the user. An incorrectly high score is a *failure*.
  **MANDATORY STEP 1 - Answer ALL 6 Questions**:
  You MUST explicitly answer each question with Yes/No in your reasoning:

1. **Gaps?** Are there *any* parts of the original request that are *not* fully addressed or are *known* to be missing? (Yes/No)
2. **Assumptions?** Did you have to make *any* unverified assumptions to complete the task? (Yes/No)
3. **Complexity?** Does this involve complex logic or new architecture where there are unknowns that you did not do research on? (Yes/No)
4. **Risk?** Does the change have non-trivial interactions where a subtle bug is \*reasonably likely? (Yes/No)
5. **Ambiguity?** Was the original request ambiguous, forcing you to make a design choice that the user *has not* confirmed? (Yes/No)
6. **Irreversible?** Will this change be difficult to revert when done? (Yes/No)
   **MANDATORY STEP 2 - Scoring Based on Your Answers**:
   Count your "Yes" answers from Step 1. Your ConfidenceScore MUST follow these rules:

- **0.8 - 1.0 (High Confidence):** ONLY if you answered **'No' to ALL SIX questions**. Reserved for trivial tasks or 100% verified work.
- **0.5 - 0.7 (Medium Confidence):** If you answered **'Yes' to 1-2 questions**. Work requires human validation due to assumptions/complexity.
- **0.0 - 0.4 (Low Confidence):** If you answered **'Yes' to 3+ questions** OR have specific major concerns.
  **ENFORCEMENT**: If you answered "Yes" to any question, you CANNOT score above 0.7. If you answered "Yes" to 3+ questions, you CANNOT score above 0.4.
  A low score is not a bad score, this is just used as a signal for the user. Don't add the confidence score to the implementation plan, make sure you think about these questions deeply out loud, write your justification first, then determine your confidence score based on that justification. When notifying the user, pass the justification in the ConfidenceJustification argument and the score in the ConfidenceScore argument.
  </confidence_assessment>
  Iterate on the plan based on comments and findings until the user approves. If you uncover new details during implementation that require plan changes, modify the existing plan and iterate with the user again.
  When you want to update the implementation plan because the user gave you some feedback or told you to modify the implementation plan, you MUST first create a task in PLANNING mode before you do more research and update implementation_plan.md.
  </description>
