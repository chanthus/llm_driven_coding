# Generate Presentation

You are generating a Marp presentation from a selected presentation folder.

---

## Step 1: List Available Presentations

First, scan the `presentations/` folder to find all available presentations:

```bash
ls -d presentations/*/
```

Each subfolder is a presentation. The structure should be:
```
presentations/
├── llm_driven_coding/
│   ├── input.md      # Source content
│   └── output/       # Generated files go here
├── another_talk/
│   ├── input.md
│   └── output/
```

---

## Step 2: Ask User to Select Presentation

Use the AskUserQuestion tool with the available presentations as options.

**Question:** "Which presentation do you want to generate?"
**Options:** List each folder name found in `presentations/`

Store the selected name as `PRESENTATION_NAME`.

---

## Step 3: Ask Generation Mode

Use the AskUserQuestion tool:

**Question:** "How should I generate the presentation?"
**Options:**
1. **Enhance existing** - Use current output/slides.md as guide
2. **Recreate from scratch** - Fresh from input.md

---

## CRITICAL RULES (DO NOT VIOLATE)

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

## Step 4: Read Source Materials

```bash
# Read the input file
cat presentations/{PRESENTATION_NAME}/input.md
```

If "Enhance existing" mode, also read:
```bash
cat presentations/{PRESENTATION_NAME}/output/slides.md
```

---

## Step 5: Create Output Folder Structure

```bash
mkdir -p presentations/{PRESENTATION_NAME}/output/images
```

---

## Step 6: Generate Art

Generate art ONLY for concepts in the source file.

Save images to `presentations/{PRESENTATION_NAME}/output/images/`:

```bash
bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "[PROMPT BASED ON SOURCE CONTENT]" \
  --size 1024x1024 \
  --output presentations/{PRESENTATION_NAME}/output/images/{name}.png
```

---

## Step 7: Create Marp Presentation

Write to `presentations/{PRESENTATION_NAME}/output/slides.md`:

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

### Image References

Images are relative to slides.md location:
```markdown
![bg right:40% contain](images/name.png)
```

---

## Step 8: Export to PowerPoint and HTML

```bash
bunx @marp-team/marp-cli \
  presentations/{PRESENTATION_NAME}/output/slides.md \
  --pptx \
  -o presentations/{PRESENTATION_NAME}/output/presentation.pptx \
  --allow-local-files \
  --no-stdin

bunx @marp-team/marp-cli \
  presentations/{PRESENTATION_NAME}/output/slides.md \
  --html \
  -o presentations/{PRESENTATION_NAME}/output/presentation.html \
  --allow-local-files \
  --no-stdin
```

---

## Step 9: Validation Checklist

Before finishing, verify:

- [ ] **Order matches source** - sections in same sequence
- [ ] **No missing content** - every bullet, code, link present
- [ ] **No invented content** - nothing added beyond source
- [ ] **Text fits slides** - no overflow, max 5 bullets
- [ ] **Images not cropped** - all use `contain`
- [ ] **Code readable** - max 8 lines per block

---

## Step 10: Open and Report

```bash
open presentations/{PRESENTATION_NAME}/output/presentation.pptx
```

Report:
- Presentation name selected
- Number of slides generated
- All source sections covered
- List of generated images
- File locations:
  - `presentations/{NAME}/output/slides.md`
  - `presentations/{NAME}/output/presentation.pptx`
  - `presentations/{NAME}/output/presentation.html`
  - `presentations/{NAME}/output/images/`

---

## Creating a New Presentation

To add a new presentation:

```bash
mkdir -p presentations/{new_name}/output/images
touch presentations/{new_name}/input.md
```

Then edit `input.md` with your content and run `/generate-presentation`.
