# Presentation Generator

## Structure

```
presentations/{name}/
├── input/
│   ├── input.md      # Source content (ONLY source of truth)
│   └── images/       # User screenshots/diagrams
└── output/
    ├── slides.md     # Generated Marp
    ├── presentation.pptx
    ├── presentation.html
    └── images/       # Copied + AI-generated

docs/                 # GitHub Pages (symlinks to output/)
```

## Commands

- `/generate-presentation` - Main workflow (uses presentation-generator skill)

## Skills

- `.claude/skills/presentation-generator/` - Full generation workflow
- `.claude/skills/art/` - AI image generation (gpt-image-1)

## Critical Rules

### Content
1. **Source file = ONLY truth** - never invent content
2. **Preserve order** - no reordering for "flow"
3. **Include everything** - every bullet, link, code block
4. **Max 5 bullets/slide, 6 words/bullet** - details go to speaker notes

### Images
- Always use `contain`: `![bg right:40% contain](images/x.png)`
- Generate at 1024x1024
- User images in `input/images/` take priority
- AI generates for slides without user images

### Author Hints
Parse hints from input.md:
- `(comment: ...)` - Speaker notes
- `<!-- umbrella header -->` - Section with morph transitions
- `<!-- Show in row -->` - Use HTML table for images

## Tech

- **Runtime:** Bun (not Node)
- **Export:** `bunx @marp-team/marp-cli --pptx --html`
- **Images:** `bun run .claude/skills/art/tools/generate-ulart-image.ts`

## Theme

- Background: `#1a1a2e`
- Headers: `#ff6b35` (orange)
- Subheaders: `#00d9ff` (cyan)
