# LLM Driven Presentations

Generate Marp presentations with AI-generated art using Claude Code.

## Setup

```bash
bun install
```

Requires `OPENAI_API_KEY` in `~/.claude/.env` for image generation.

## Usage

1. Create a presentation folder:
   ```bash
   mkdir -p presentations/{name}/input/images
   mkdir -p presentations/{name}/output/images
   ```

2. Write content in `presentations/{name}/input/input.md`

3. Run `/generate-presentation` in Claude Code

4. Output appears in `presentations/{name}/output/`:
   - `slides.md` - Marp source
   - `presentation.pptx` - PowerPoint
   - `presentation.html` - Web (with transitions)

## Input Format

Write markdown with your content. Add hints for layout:

```markdown
# My Talk

## Section One
- Point one
- Point two ![](images/screenshot.png)

### Subsection <!-- use this as umbrella header -->
- Detail with image ![](images/diagram.png)
```

Place screenshots in `input/images/` - they'll be preserved. AI generates art for slides without images.

## Tech Stack

- **Runtime:** Bun
- **Slides:** Marp CLI
- **Images:** OpenAI gpt-image-1
