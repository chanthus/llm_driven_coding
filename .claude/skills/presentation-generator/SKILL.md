---
name: presentation-generator
description: |
  Generate visually rich Marp presentations from input.md source files.
  Be generous with AI-generated art - aim for 50-70% of slides with images.
  Use creative visual metaphors. Subtle professional transitions (HTML).
  Exports to PPTX and HTML.

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
│   ├── input/
│   │   └── input.md  # Source content (write here)
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
cat presentations/{NAME}/input/input.md
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

**IMPORTANT: Always generate images in parallel using background processes (`&`) and `wait`.**

1. **Plan all images first** - Before generating, list all images needed with their prompts
2. **Use a SINGLE Bash command** - Background each generation with `&`, then `wait` for all
3. **Batch in groups of 4-5** - Avoid API rate limits while maximizing parallelism

**Example batch (4 images in parallel):**

```bash
bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Hero image prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/hero.png &

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Architecture diagram prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/architecture.png &

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Workflow visualization prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/workflow.png &

bun run .claude/skills/art/tools/generate-ulart-image.ts \
  --model gpt-image-1 \
  --prompt "Comparison visual prompt..." \
  --size 1024x1024 \
  --output presentations/{NAME}/output/images/comparison.png &

wait
echo "All images generated!"
```

**Key syntax:**
- End each command with `&` to run in background
- Use `wait` at the end to wait for ALL background jobs to complete
- All commands in a SINGLE Bash tool call

**Parallelization strategy:**

| Total Images | Strategy |
|--------------|----------|
| 1-4 | Single Bash call with `&` and `wait` |
| 5-8 | Two Bash calls, 4 each with `&` and `wait` |
| 9-12 | Three Bash calls, 4 each |
| 13+ | Continue in batches of 4-5 |

**Why batch size of 4-5?**
- Avoids API rate limits
- Balances speed vs reliability
- Single `wait` ensures all complete before proceeding

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
- [ ] **Transitions:** Default fade set, section headers use push/slide

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
- Every fact MUST come from `presentations/{NAME}/input/input.md`

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

## Slide Transitions (HTML only)

**Requirements:**
- Only works in HTML export (not PPTX/PDF)
- Requires View Transitions API: Chrome 111+, Edge 111+, Safari 18.2+, Firefox 144+
- Marp CLI v2.4.0+ (we use v4.2.3)

### How Transitions Work

The `transition` directive applies to the **NEXT slide boundary** (the `---` separator).

```markdown
---
transition: fade
---

# Slide 1
This slide will FADE OUT when leaving

---

# Slide 2
Arrived here with fade effect
```

### Global Default Transition

Set in frontmatter (applies to all slides):

```markdown
---
marp: true
transition: fade
---
```

### Per-Slide Transitions

Use scoped directive `_transition` to override for ONE slide:

```markdown
# Slide 1

Content here...

<!-- _transition: push -->

---

# Slide 2
Arrived with push effect (only this transition)
```

**Important:** The directive goes BEFORE the `---`, not after!

### Built-in Transitions (33 total)

| Category | Transitions |
|----------|-------------|
| **Subtle** | `fade`, `fade-out`, `dissolve` |
| **Directional** | `slide`, `push`, `pull`, `cover`, `reveal` |
| **Wipes** | `wipe`, `wiper`, `swipe`, `swoosh` |
| **3D Effects** | `cube`, `cylinder`, `flip`, `rotate`, `pivot` |
| **Dramatic** | `zoom`, `drop`, `fall`, `explode`, `implode`, `melt` |
| **Shapes** | `diamond`, `star`, `iris-in`, `iris-out`, `clockwise`, `counterclockwise` |
| **Other** | `coverflow`, `glow`, `in-out`, `overlap`, `swap`, `none` |

### Duration Control

Default is 0.5s. Customize with space-separated syntax:

```markdown
transition: fade 0.3s
transition: push 300ms
transition: zoom 1s
```

### Recommended Usage

| Context | Transition | Duration |
|---------|------------|----------|
| **Default** | `fade` | 0.3s |
| **Section change** | `push` or `slide` | 0.4s |
| **Reveal** | `reveal` or `cover` | 0.3s |
| **Conclusion** | `fade-out` | 0.5s |
| **Key point** | `zoom` | 0.3s (sparingly) |

### Creating Custom Transitions

Define custom animations with CSS `@keyframes` in the style block:

#### Simple Custom Transition (auto-reversed for incoming)

```markdown
---
marp: true
transition: dissolve
style: |
  @keyframes marp-transition-dissolve {
    from { opacity: 1; }
    to { opacity: 0; }
  }
---
```

#### Split Animation (different for outgoing/incoming)

```markdown
style: |
  @keyframes marp-outgoing-transition-slide-up {
    from { transform: translateY(0); }
    to { transform: translateY(-100%); }
  }
  @keyframes marp-incoming-transition-slide-up {
    from { transform: translateY(100%); }
    to { transform: translateY(0); }
  }
```

#### Direction-Aware (reverses on backward navigation)

Use `--marp-transition-direction` (1 = forward, -1 = backward):

```markdown
style: |
  @keyframes marp-outgoing-transition-slide-left {
    from { transform: translateX(0); }
    to { transform: translateX(calc(var(--marp-transition-direction, 1) * -100%)); }
  }
  @keyframes marp-incoming-transition-slide-left {
    from { transform: translateX(calc(var(--marp-transition-direction, 1) * 100%)); }
    to { transform: translateX(0); }
  }
```

### Morphing Animations (PowerPoint Morph-like)

Use `view-transition-name` to smoothly morph elements between slides. This creates professional, polished transitions where elements visually "travel" from one state to another.

#### When to Use Morphing

| Scenario | Example |
|----------|---------|
| **List → Detail** | Item in a list morphs into the title of the detail slide |
| **Overview → Zoom** | Diagram element expands to fill the next slide |
| **Progress indicators** | Icon moves position to show advancement |
| **Consistent elements** | Logo, headers, or images that appear on multiple slides |

#### Basic Example

```markdown
---

# Features

<h2 style="view-transition-name: feature-title;">Sub Agents</h2>

- Specialized instances
- Custom rules

---

# Sub Agents
<!-- The h1 below morphs FROM the h2 above -->

<h1 style="view-transition-name: feature-title;">Sub Agents</h1>

Deep dive into configuration...
```

#### Image Morphing (Zoom Effect)

```markdown
---

# Overview

![height:150px](images/diagram.png)
<!-- Add inline style for morph -->
<style scoped>
img { view-transition-name: main-diagram; }
</style>

---

# Diagram Details

![height:400px](images/diagram.png)
<style scoped>
img { view-transition-name: main-diagram; }
</style>
```

#### Professional Morphing Guidelines

1. **Subtle is better** — Small position/size changes feel natural
2. **Same content** — Morph elements that are conceptually the same
3. **Unique names** — Each `view-transition-name` must be unique per slide
4. **Limit usage** — 1-2 morphing elements per transition max
5. **Test thoroughly** — Works best in Chrome/Edge; Safari has bugs

#### Don't Morph

- Unrelated elements (confuses viewers)
- Too many elements at once (overwhelming)
- Across many slides (loses impact)

**Browser support:** Chrome 111+, Edge 111+. Safari has known bugs with morphing.

### Professional Guidelines

1. **Less is more** — Default to `fade`, only change with purpose
2. **Consistency** — Same transition for similar slide types
3. **Speed** — 0.3-0.4s is snappy; >0.5s feels slow
4. **Avoid flashy** — `cube`, `coverflow`, `explode` can distract
5. **Test in browser** — Transitions only visible in HTML export

---

## Marp Template

```markdown
---
marp: true
theme: default
paginate: true
transition: fade 0.3s
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
mkdir -p presentations/{new_name}/input
mkdir -p presentations/{new_name}/output/images
touch presentations/{new_name}/input/input.md
```

Then edit `input/input.md` with your content and run `/generate-presentation`.

---

## Theme

- Dark background: `#1a1a2e`
- Orange headers: `#ff6b35`
- Cyan subheaders: `#00d9ff`
- White text on dark

---

## Art Ideas by Slide Type

| Slide Type | Visual Ideas | Transition |
|------------|--------------|------------|
| Title/Hero | Abstract representation of main topic | `fade` (default) |
| Section header | Icon or symbol for section theme | `push` or `slide` |
| Concept explanation | Visual metaphor, abstract diagram | `fade` |
| List of features | Grid of icons, connected elements | `fade` |
| Code example | Can skip OR "code flow" visual | `fade` |
| Comparison | Split, before/after, versus layout | `wipe` or `reveal` |
| Process/workflow | Connected nodes, pipeline, flow | `fade` |
| Benefits/advantages | Upward arrows, growth imagery | `fade` |
| Problems/challenges | Obstacles, tangles, warning | `fade` |
| Tools/technologies | Tool icons, gears, building blocks | `fade` |
| Thank you/Q&A | Reuse hero image | `fade-out` |

**Remember:** When in doubt, generate an image. Visual slides are more engaging than text-only slides.
