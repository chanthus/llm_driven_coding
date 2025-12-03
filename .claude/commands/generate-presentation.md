# Generate Presentation

Use the **presentation-generator** skill to create a Marp presentation.

## Instructions

1. Read the skill: `.claude/skills/presentation-generator/SKILL.md`
2. Follow the workflow steps exactly
3. Use the art skill for image generation
4. Export to PPTX and HTML
5. **Update root `index.html`** with new/updated presentation

## Quick Reference

```bash
# List presentations
ls -d presentations/*/

# Generate image
bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/{name}.png

# Export
bunx @marp-team/marp-cli presentations/{NAME}/output/slides.md \
  --pptx -o presentations/{NAME}/output/presentation.pptx \
  --allow-local-files --no-stdin
```

## Critical Rules

- Source file is the ONLY source of truth
- Preserve exact order from input
- Max 5 bullets, 6 words each
- Always use `contain` for images
- Generate at 1024x1024

## Update Root index.html

After generating a presentation, update `/index.html` to include it in the presentations list.

The `index.html` has a JavaScript array of presentations:

```javascript
const presentations = [
  {
    id: 'llm_driven_coding',           // Folder name in presentations/
    title: 'LLM Driven Coding',        // Display title
    description: 'Focused on Claude Code - tips, tricks, and best practices',
    updated: '2025-12-03'              // YYYY-MM-DD format
  }
  // Add new presentations here
];
```

**For each presentation folder in `presentations/`:**
1. Check if it exists in the array
2. If not, add it with appropriate title, description (derive from input.md), and today's date
3. If it exists, **update the `updated` date to today** (YYYY-MM-DD format)
4. Links automatically point to `presentations/{id}/output/presentation.html`

$ARGUMENTS
