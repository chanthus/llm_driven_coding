# Presentation Generator

A project for generating Marp presentations with AI-generated art.

## Project Structure

```
presentations/                    # All presentations live here
├── llm_driven_coding/           # Example presentation
│   ├── input.md                 # Source content (write here)
│   └── output/                  # Generated files
│       ├── slides.md            # Marp source
│       ├── presentation.pptx    # PowerPoint export
│       ├── presentation.html    # Web presentation
│       └── images/              # Generated art
├── another_talk/                # Another presentation
│   ├── input.md
│   └── output/
.claude/
├── commands/
│   └── generate-presentation.md # Main command
└── skills/
    ├── art/                     # Image generation skill
    └── presentation-generator/  # Presentation workflow skill
```

## Tech Stack

- **Runtime:** Bun (not Node.js)
- **Presentations:** Marp CLI (`@marp-team/marp-cli`)
- **Image Generation:** OpenAI gpt-image-1 via art skill
- **Package Manager:** Bun

## Commands

### Generate Presentation
```
/generate-presentation
```
1. Lists available presentations in `presentations/`
2. Asks which one to generate
3. Asks: enhance existing or recreate from scratch
4. Generates art using the art skill
5. Exports to PPTX and HTML

### Create New Presentation
```bash
mkdir -p presentations/{name}/output/images
touch presentations/{name}/input.md
```
Then edit `input.md` and run `/generate-presentation`.

### Manual Marp Export
```bash
bunx @marp-team/marp-cli \
  presentations/{name}/output/slides.md \
  --pptx \
  -o presentations/{name}/output/presentation.pptx \
  --allow-local-files --no-stdin
```

## Skills

### Presentation Generator Skill (`.claude/skills/presentation-generator/`)
Complete workflow for generating Marp presentations:
- Source file validation
- Art generation coordination
- Slide creation with Marp template
- Export to PPTX and HTML

### Art Skill (`.claude/skills/art/`)
Generates images using the PAI aesthetic (Tron-meets-Excalidraw style):
- Dark slate backgrounds
- Neon orange (#ff6b35) and cyan (#00d9ff) accents
- Hand-drawn sketch aesthetic

**Workflows available:**
- `technical-diagrams.md` - Architecture diagrams
- `timelines.md` - Chronological progressions
- `comparisons.md` - X vs Y layouts
- `frameworks.md` - 2x2 matrices
- `taxonomies.md` - Classification grids

**Usage:**
```bash
bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "..." \
  --size 1024x1024 \
  --output presentations/{name}/output/images/diagram.png
```

## Rules

### Bun Over Node
- Use `bun <file>` instead of `node <file>`
- Use `bun test` instead of jest/vitest
- Use `bun install` instead of npm/yarn
- Bun auto-loads `.env` - no dotenv needed

### Presentation Style
- Dark theme (background: #1a1a2e)
- Orange headers (#ff6b35)
- Cyan subheaders (#00d9ff)

### Presentation Content Rules (CRITICAL)
1. **Source file is the ONLY source of truth** - do not invent or add information
2. **Preserve exact order** from input file - no reordering for "flow"
3. **Do not miss any content** - every bullet, code block, link must appear
4. **Max 5 bullets per slide** - split into multiple slides if needed
5. **Max 6 words per bullet** - details go in speaker notes
6. **Max 8 lines per code block** - truncate with "..." if longer

### Image Rules (Prevent Cropping)
- Use `![bg right:40% contain](images/name.png)` for side images
- Use `![bg contain](images/name.png)` for full backgrounds
- Use `![height:350px](images/name.png)` for inline images
- **NEVER** use `![bg]` without `contain` - causes cropping
- Generate at **1024x1024** (square) for better fit

## Environment

API keys should be in `~/.claude/.env`:
```
OPENAI_API_KEY=sk-...
```

## Workflow

1. Create or edit `presentations/{name}/input.md`
2. Run `/generate-presentation`
3. Select which presentation to generate
4. Choose: enhance or recreate
5. Review output in `presentations/{name}/output/`
6. Make manual tweaks to `slides.md` if needed
7. Re-export with Marp if you edited slides.md directly
