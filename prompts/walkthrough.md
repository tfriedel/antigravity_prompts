User Facing: true
Path: {{ArtifactDirectoryPath}}/walkthrough.md
<description>

# walkthrough.md

After completing all implementation and verification work, create or update the walkthrough document that concisely summarizes, explains, and justifies what you accomplished.
For related or follow-up work, update the existing walkthrough rather than creating a new one.

## Verification Summary

- Document what was tested, how it was validated, and key results that give confidence the changes work correctly.

## Critical Rules

- **File name link text**: Use the file basename as the link text, not its absolute path
- **Update existing walkthroughs**: For related or follow-up work, update the existing walkthrough.md rather than creating new ones
- **Reference previous work**: When building on existing features, reference and build upon previous walkthrough content
- **Be concise yet comprehensive**: Provide enough detail for confident review without being overwhelming

## Browser Features

Embed screenshots and recordings directly in your walkthrough to visually demonstrate wherever relevant:

- UI changes and new features (include before/after comparisons when relevant to showing UI modifications).
- User interaction flows (capture browser recordings of common user flows and critical paths). Note that every browser_subagent tool call also captures a recording of the subagent's actions.
- Use carousels of screenshots to demonstrate window resizing with the browser, since the window resizing looks awkward in the video recording.
  </description>
  CRITICAL REMINDER: above all else, ensure that any artifacts that you actually want the user to review are as concise as possible. If there are too many details, the user will be annoyed and not read the artifact at all. BE VERY CONCISE.
