---
name: presentation-generator
description: |
  Generate visually rich Marp presentations from input.md source files.
  Be generous with AI-generated art - aim for 50-70% of slides with images.
  Use creative visual metaphors. Exports to PPTX and HTML.

triggers:
  - USE WHEN user wants to generate a presentation
  - USE WHEN user wants to create slides from content
  - USE WHEN user mentions Marp, PowerPoint, or presentation generation
---

# Presentation Generator Skill

Generate Marp presentations with AI-generated art from source content.

---

## Project Structure

```
presentations/
├── {name}/
│   ├── input.md      # Source content (write here)
│   └── output/       # Generated files
│       ├── slides.md            # Marp source
│       ├── presentation.pptx    # PowerPoint export
│       ├── presentation.html    # Web presentation
│       └── images/              # Generated art
```

---

## Workflow

### Step 1: List Available Presentations

```bash
ls -d presentations/*/
```

### Step 2: Ask User to Select

Use AskUserQuestion:
- **Question:** "Which presentation do you want to generate?"
- **Options:** Each folder name from `presentations/`

### Step 3: Ask Generation Mode

Use AskUserQuestion:
- **Enhance existing** - Use current output/slides.md as guide
- **Recreate from scratch** - Fresh from input.md only

### Step 4: Read Source Materials

```bash
cat presentations/{NAME}/input.md
```

If enhancing, also read existing slides.md.

### Step 5: Create Output Structure

```bash
mkdir -p presentations/{NAME}/output/images
```

### Step 6: Generate Art (Be Generous!)

**Goal: Make the presentation visually rich and engaging.**

Generate art for as many slides as possible. Be creative with visual metaphors!

#### When to Generate Images

- **Always:** Title slides, section headers, key concepts
- **Strongly encouraged:** Any slide with abstract ideas, processes, comparisons
- **Consider:** Even simple bullet slides benefit from supporting visuals
- **Aim for:** At least 50-70% of slides should have images

#### Creative Approaches

Think beyond literal illustrations:

- **Metaphors:** "Context length problem" → overflowing container, memory gauge
- **Abstract concepts:** "Sub agents" → team of specialized robots, delegation flow
- **Processes:** "Workflow" → connected nodes, pipeline visualization
- **Comparisons:** "Before/After" → split compositions, contrast imagery
- **Lists:** Group related items into a single conceptual image

#### Parallel Image Generation (DEFAULT)

**IMPORTANT: Always generate images in parallel batches to speed up generation.**

1. **Plan all images first** - Before generating, list all images needed with their prompts
2. **Batch in groups of 4-5** - Issue multiple Bash tool calls in a single response
3. **No dependencies** - Image generations are independent, perfect for parallelization

**Example batch (4 images in one response):**

```bash
# These 4 commands run SIMULTANEOUSLY when issued in a single response:

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Hero image prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/hero.png

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Architecture diagram prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/architecture.png

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Workflow visualization prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/workflow.png

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Comparison visual prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/comparison.png
```

**Parallelization strategy:**

| Total Images | Strategy |
|--------------|----------|
| 1-4 | Single batch, all parallel |
| 5-8 | Two batches of 4 |
| 9-12 | Three batches of 4 |
| 13+ | Batches of 4-5, continue until done |

**Why batch size of 4-5?**
- Avoids API rate limits
- Balances speed vs reliability
- Easy to track progress

#### Prompt Tips

- Describe the **feeling** and **concept**, not just objects
- Include aesthetic keywords: "Tron-meets-Excalidraw", "hand-drawn sketch"
- Specify colors: neon orange (#ff6b35), cyan (#00d9ff), dark slate
- Add mood: "minimal", "clean", "abstract", "subtle glows"
- End with: "No text or labels"

### Step 7: Create Marp Slides

Write to `presentations/{NAME}/output/slides.md` with the template below.

### Step 8: Export

```bash
bunx @marp-team/marp-cli \
  presentations/{NAME}/output/slides.md \
  --pptx \
  -o presentations/{NAME}/output/presentation.pptx \
  --allow-local-files --no-stdin

bunx @marp-team/marp-cli \
  presentations/{NAME}/output/slides.md \
  --html \
  -o presentations/{NAME}/output/presentation.html \
  --allow-local-files --no-stdin
```

### Step 9: Validate

- [ ] Order matches source
- [ ] No missing content
- [ ] No invented content
- [ ] Text fits slides
- [ ] Images not cropped
- [ ] Code readable
- [ ] **Art coverage:** 50-70% of slides have images
- [ ] **Visual variety:** Different image types used (diagrams, metaphors, icons)

### Step 10: Open and Report

```bash
open presentations/{NAME}/output/presentation.pptx
```

---

## CRITICAL RULES

### 1. Source File is the ONLY Source of Truth

- **DO NOT** add information not in the input file
- **DO NOT** invent, embellish, or expand beyond the source
- **DO NOT** add your own examples or recommendations
- If source has "TODO" sections, show as "Coming Soon"
- Every fact MUST come from `presentations/{NAME}/input.md`

### 2. Preserve Order from Input File

- Follow the EXACT section order from the source
- Do not reorder sections for "better flow"
- Input file structure = presentation structure

### 3. Do Not Miss Any Information

- Every bullet point → appears in presentation
- Every code example → appears in presentation
- Every link → appears in presentation
- Split into multiple slides if needed (Part 1, Part 2)

### 4. Text Must Stay Within Slide Boundaries

- **Maximum 5 bullet points per slide**
- **Maximum 6 words per bullet point**
- **Code blocks: maximum 8 lines**
- Move details to **speaker notes**

### 5. Images Must Not Be Cut Off

- Use `![bg right:40% contain](images/name.png)` for side images
- Use `![bg contain](images/name.png)` for backgrounds
- Use `![height:350px](images/name.png)` for inline
- **NEVER** use `![bg]` without `contain`
- Generate images at **1024x1024** (square)

---

## Marp Template

```markdown
---
marp: true
theme: default
paginate: true
backgroundColor: #1a1a2e
color: #ffffff
style: |
  section {
    font-family: 'Segoe UI', Arial, sans-serif;
    padding: 40px;
  }
  h1 {
    color: #ff6b35;
    font-size: 2.2em;
    margin-bottom: 0.5em;
  }
  h2 {
    color: #00d9ff;
    font-size: 1.6em;
  }
  h3 {
    color: #ff6b35;
    font-size: 1.3em;
  }
  ul {
    font-size: 0.95em;
    line-height: 1.4;
  }
  li {
    margin-bottom: 0.3em;
  }
  code {
    background-color: #2d2d2d;
    padding: 2px 6px;
    border-radius: 4px;
    font-size: 0.85em;
  }
  pre {
    font-size: 0.75em;
    max-height: 300px;
    overflow: hidden;
  }
  img {
    max-height: 350px;
    object-fit: contain;
  }
  a {
    color: #00d9ff;
  }
---

# Slide Title

- Short bullet (max 6 words)
- Another short bullet

![bg right:40% contain](images/image.png)

<!--
Speaker notes with details here.
-->

---
```

---

## Image References

Images are relative to slides.md location:

```markdown
![bg right:40% contain](images/name.png)
```

---

## Creating a New Presentation

```bash
mkdir -p presentations/{new_name}/output/images
touch presentations/{new_name}/input.md
```

Then edit `input.md` with your content and run `/generate-presentation`.

---

## Theme

- Dark background: `#1a1a2e`
- Orange headers: `#ff6b35`
- Cyan subheaders: `#00d9ff`
- White text on dark

---

## Art Ideas by Slide Type

| Slide Type | Visual Ideas |
|------------|--------------|
| Title/Hero | Abstract representation of main topic, dramatic composition |
| Section header | Icon or symbol for that section's theme |
| Concept explanation | Visual metaphor, abstract diagram |
| List of features | Grid of icons, connected elements |
| Code example | Can skip OR show abstract "code flow" visual |
| Comparison | Split composition, before/after, versus layout |
| Process/workflow | Connected nodes, pipeline, flow diagram |
| Benefits/advantages | Upward arrows, growth imagery, positive symbols |
| Problems/challenges | Obstacles, tangles, warning imagery |
| Tools/technologies | Tool icons, gear compositions, building blocks |
| Thank you/Q&A | Reuse hero image or create closing visual |

**Remember:** When in doubt, generate an image. Visual slides are more engaging than text-only slides.
