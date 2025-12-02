# Generate Presentation

Use the **presentation-generator** skill to create a Marp presentation.

## Instructions

1. Read the skill: `.claude/skills/presentation-generator/SKILL.md`
2. Follow the workflow steps exactly
3. Use the art skill for image generation
4. Export to PPTX and HTML

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
