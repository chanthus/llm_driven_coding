---
name: presentation-generator
description: |
  Generate visually rich Marp presentations from input.md source files.
  EVERY slide must have an image - user-provided or AI-generated.
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
│   │   ├── input.md  # Source content (write here)
│   │   └── images/   # User-provided images (screenshots, diagrams)
│   └── output/       # Generated files
│       ├── slides.md            # Marp source
│       ├── presentation.pptx    # PowerPoint export
│       ├── presentation.html    # Web presentation
│       └── images/              # All images (copied + generated)
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

### Step 4.5: Parse Author Hints & Comments

**Scan input.md for embedded hints that guide presentation generation.**

Authors can include hints in the input file using these formats:

#### Comment Syntax (recognized patterns)

| Pattern | Purpose | Example |
|---------|---------|---------|
| `(comment: ...)` | General notes, context | `(comment: this applies to other tools too)` |
| `(hint: ...)` | Layout/style suggestions | `(hint: put these in a table)` |
| `(transition: ...)` | Slide transition override | `(transition: zoom)` |
| `(layout: ...)` | Image/content layout | `(layout: 2x2 grid)` |
| `(animation: ...)` | Animation suggestion | `(animation: morph these images)` |
| `<!-- ... -->` | HTML comments | `<!-- use reveal transition here -->` |

#### How to Handle Each Hint Type

**1. General Comments `(comment: ...)`**
- These are author notes for context
- May inform speaker notes but usually NOT shown on slides
- Example: `(comment: I'm guessing this applies to other agents too)` → Add to speaker notes

**2. Layout Hints `(hint: ...)` or `(layout: ...)`**

| Hint | How to Implement |
|------|------------------|
| `put these in a table` | Use markdown table for the items |
| `2x2 grid` | Use HTML grid or 2-column layout |
| `side by side` | Use `![bg left:50%]` and `![bg right:50%]` |
| `inline images` | Use `![height:150px]` for each, no bg |
| `full width` | Use `![bg contain]` |
| `compare these` | Split layout with before/after |

**3. Transition Hints `(transition: ...)` or `(animation: ...)`**

Apply the specified transition to that slide:
```markdown
<!-- _transition: zoom -->
```

Common requested transitions:
- `zoom` - Dramatic emphasis
- `push` / `slide` - Section changes
- `reveal` / `cover` - Unveiling content
- `morph` - Requires `view-transition-name` on elements

**4. Image Layout Hints**

When author specifies how multiple images should be displayed:

| Hint | Implementation |
|------|----------------|
| `table` or `grid` | Use HTML table with images in cells |
| `row` or `inline` | Multiple `![height:Xpx]` images inline |
| `sequence` or `steps` | Numbered with `1.`, `2.`, etc. before each |
| `morph` | Apply `view-transition-name` to morph between slides |

**Example: Images in a Table**

Input:
```markdown
### Bigger Feature
(hint: put these images in a table)
![](images/step1.png) ![](images/step2.png) ![](images/step3.png)
```

Output in slides.md:
```markdown
---

# Bigger Feature

<table>
<tr>
<td>

![height:200px](images/step1.png)

</td>
<td>

![height:200px](images/step2.png)

</td>
<td>

![height:200px](images/step3.png)

</td>
</tr>
</table>

---
```

**Example: Morphing Images**

Input:
```markdown
### Process Steps
(animation: morph these images)
![](images/before.png)
![](images/after.png)
```

Output: Create two slides with matching `view-transition-name`:
```markdown
---

# Process - Before

![height:350px](images/before.png)
<style scoped>
img { view-transition-name: process-morph; }
</style>

---

# Process - After

![height:350px](images/after.png)
<style scoped>
img { view-transition-name: process-morph; }
</style>

---
```

#### Extracting Hints

```bash
# Find all hints in input.md
grep -n -E '\((comment|hint|transition|layout|animation):' presentations/{NAME}/input/input.md
grep -n '<!--.*-->' presentations/{NAME}/input/input.md
```

#### Important Rules for Hints

1. **Hints are instructions, not content** - Never display hint text on slides
2. **Hints apply locally** - A hint affects only the nearest content (same section/bullet)
3. **Conflicting hints** - Later hint wins, or ask user for clarification
4. **Unknown hints** - Treat as speaker notes; don't ignore silently
5. **Preserve author intent** - Hints reveal what the author wants; honor them

### Step 5: Create Output Structure & Copy Input Images

```bash
mkdir -p presentations/{NAME}/output/images

# Copy user-provided images from input to output (if they exist)
if [ -d "presentations/{NAME}/input/images" ]; then
  cp -r presentations/{NAME}/input/images/* presentations/{NAME}/output/images/
fi
```

**Important:** User-provided images take priority. These are often screenshots, diagrams, or specific visuals the user wants to include.

### Step 6: Generate Art (Be Generous!)

**Goal: Make the presentation visually rich and engaging.**

#### Priority: User-Provided Images First

**CRITICAL: User images MUST be preserved exactly where they appear in input.md**

1. **Scan input.md for ALL image references** like `![](images/screenshot.png)`
2. **List each image and its surrounding text** - this determines which slide it goes on
3. **Keep images on the SAME slide as their associated text**
4. **Only generate AI art** for slides that have NO user-provided images nearby

#### Handling Input Images (MANDATORY)

**Step-by-step process:**

1. **Extract all image references from input.md:**
   ```
   grep -n "!\[\]" presentations/{NAME}/input/input.md
   ```

2. **For each image, note the surrounding context:**
   - What bullet point or heading is it near?
   - Is it inline with text or standalone?
   - This context determines which slide the image belongs on

3. **Map images to slides:**
   - If image is after "Sub agents" section header → put on Sub Agents slide
   - If image is inline with "Use drizzle" bullet → put on the slide with that bullet
   - If image is standalone after a list → put on the slide with that list

4. **URL-encode filenames with spaces (CRITICAL):**

   **Filenames with spaces MUST be URL-encoded in slides.md or images will break!**

   - `images/my image.png` → `images/my%20image.png`
   - `images/root claudemd.png` → `images/root%20claudemd.png`
   - Replace ALL spaces with `%20`

   **Wrong:** `![bg right:40% contain](images/my image.png)` ❌ (breaks markdown)
   **Right:** `![bg right:40% contain](images/my%20image.png)` ✓

5. **Format in slides.md:**
   - `![bg right:40% contain](images/my%20image.png)` for side placement
   - `![height:350px](images/my%20image.png)` for inline/centered
   - ALWAYS URL-encode spaces as `%20`

**Example mapping:**
```
Input line 24: "- Always have a root claude.md. ![](images/root%20claudemd.png)"
→ Slide about "root claude.md" MUST include "images/root claudemd.png"

Input line 47: "![](images/create%20agents.png)" (after "### Sub agents")
→ Sub Agents section slide MUST include "images/create agents.png"
```

#### When to Generate AI Images

**RULE: EVERY slide must have an image. No exceptions.**

- Generate AI art for ANY slide that doesn't have a user-provided image
- This makes presentations more fun and visually engaging
- Even simple bullet slides benefit from supporting visuals

**Which slides need AI images:**
- Title/hero slide (unless user provided one)
- Section headers without user images
- Bullet point slides without user images
- Code example slides (add "code flow" or abstract visuals)
- Links/documentation slides
- Coming Soon / TODO slides

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
- [ ] **User images:** ALL images from input.md are used
- [ ] **Image placement:** Each user image is on the same slide as its associated text
- [ ] **Art coverage:** 100% of slides have images (user-provided OR AI-generated)
- [ ] **Transitions:** Default fade set, section headers use push/slide
- [ ] **Author hints:** All hints processed and applied correctly
- [ ] **No hint leakage:** Hint syntax (comment:, hint:, etc.) does NOT appear in slides

**User Image Validation Command:**
```bash
# List images referenced in input.md
grep -o 'images/[^)]*' presentations/{NAME}/input/input.md | sed 's/%20/ /g'

# Verify each appears in slides.md
grep -o 'images/[^)]*' presentations/{NAME}/output/slides.md
```

**Hint Validation Commands:**
```bash
# List all hints in input.md (should all be processed)
grep -E '\((comment|hint|transition|layout|animation):' presentations/{NAME}/input/input.md

# Verify NO hints leaked into slides.md (should return nothing)
grep -E '\((comment|hint|transition|layout|animation):' presentations/{NAME}/output/slides.md
# ^ If this returns anything, hints were not properly stripped!
```

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
- Every link → appears in presentation **ON A SLIDE** (not just in speaker notes)
- Split into multiple slides if needed (Part 1, Part 2)

### 4. Never Remove Links

- **ALL URLs from input.md MUST appear as clickable links on slides**
- Links should NOT be hidden in speaker notes only
- If a section has multiple links, create a dedicated "Links" slide
- Use markdown link syntax: `[text](url)`
- Example: `[Sub agents](https://code.claude.com/docs/en/sub-agents)`

### 5. Never Oversimplify Text

- **Preserve the original wording** from input.md
- Do NOT reduce "Specialised coding instances with custom rules" to "Specialized instances"
- Do NOT reduce "Wrapper for commonly used prompts" to "Prompt wrappers"
- You may slightly enhance or clarify, but NEVER remove meaning
- If text is too long for a bullet, split across multiple bullets or slides
- Move extra details to speaker notes, but keep core meaning on slide

### 6. Text Must Stay Within Slide Boundaries

- **Maximum 5 bullet points per slide**
- **Maximum 6 words per bullet point**
- **Code blocks: maximum 8 lines**
- Move details to **speaker notes**

### 7. Images Must Not Be Cut Off

- Use `![bg right:40% contain](images/name.png)` for side images
- Use `![bg contain](images/name.png)` for backgrounds
- Use `![height:350px](images/name.png)` for inline
- **NEVER** use `![bg]` without `contain`
- Generate all images at **1024x1024**

### 8. Every Slide Must Have an Image

- **NO slide should be text-only** - always add a visual
- If a slide has user-provided image → use it
- If a slide has NO user image → generate AI art for it
- This makes presentations more fun and engaging

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

## Section Headings with Morph (REQUIRED)

**Every presentation should use persistent "umbrella" section headings that morph between slides.**

### Concept: Umbrella Headings & Child Slides

Think of presentations as hierarchical:
- **Umbrella heading** = Major section (e.g., "MCP", "Sub Agents", "My Setup")
- **Child slides** = Individual topics within that section

The umbrella heading should **persist visually** across all child slides, creating:
- **Context** - Viewer always knows which section they're in
- **Visual continuity** - Heading morphs smoothly from intro to breadcrumb position
- **Professional feel** - Similar to PowerPoint's section zoom feature

### How It Works

1. **Section intro slide** - Large h1 umbrella heading centered, with section image
2. **Child slides** - Same heading appears smaller (h3) as breadcrumb at top
3. **Morph transition** - The heading visually "shrinks and travels" from center to top-left

### Section Intro Slide Pattern

```markdown
---

<!-- _transition: push -->

<h1 style="view-transition-name: section-mcp; font-size: 2.5em;">MCP</h1>

## Model Context Protocol

![bg right:40% contain](images/mcp.png)

---
```

### Child Slide Pattern

```markdown
---

<h3 style="view-transition-name: section-mcp;" class="section-breadcrumb">MCP</h3>

# Serena

Uses Language Server Protocol (LSP) for semantic search

![bg right:40% contain](images/serena.png)

---
```

**Note:** The `section-breadcrumb` class styles the heading as a subtle breadcrumb (smaller, dimmed, with underline).

### Key Rules

1. **Same `view-transition-name`** - Use consistent name within section (e.g., `section-mcp`)
2. **Different sizes** - Intro slide uses large h1, child slides use smaller h3
3. **Breadcrumb class** - Child slides use `class="section-breadcrumb"` for styling
4. **Position change** - h1 is centered, h3 floats to top as a "breadcrumb"
5. **Push transition** - Use `push` for section intro, `fade` for children
6. **Reset per section** - Each major section gets its own transition name

### Identifying Umbrella Headings in Input

Look for structural patterns in `input.md`:

```markdown
## Claude Code Basics        <-- UMBRELLA (has child topics below)
- Claude.MD
- Sub agents
- Commands

### Claude.MD               <-- Child topic under "Claude Code Basics"
- Always have a root claude.md

### Sub agents              <-- Another child topic
- Specialised coding instances
```

**Umbrella indicators:**
- `##` headings with multiple `###` sub-sections underneath
- Headings that introduce a group of related topics
- Headings that appear in a table of contents or agenda

**NOT umbrellas:**
- Single standalone topics
- The final "Thank You" slide
- Title/hero slide

### Section Naming Convention

| Section | Transition Name |
|---------|-----------------|
| Claude Code Basics | `section-basics` |
| My Setup | `section-setup` |
| Sub Agents | `section-agents` |
| MCP | `section-mcp` |
| Plugins | `section-plugins` |
| Context Length | `section-context` |

### Visual Result

```
[Section Intro]          [Child Slide 1]         [Child Slide 2]
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │ MCP             │     │ MCP             │
│      MCP        │ --> │ ─────────────── │ --> │ ─────────────── │
│  Model Context  │     │ # Serena        │     │ # Playwright    │
│    Protocol     │     │ Uses LSP...     │     │ Browser auto... │
│         [img]   │     │         [img]   │     │         [img]   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
     (h1 large)           (h3 small, top)         (h3 small, top)
```

The h1 "MCP" morphs into the h3 "MCP" breadcrumb, creating visual continuity.

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
  /* Section breadcrumb style - for umbrella headings on child slides */
  .section-breadcrumb {
    font-size: 0.85em;
    opacity: 0.7;
    margin-bottom: 0.3em;
    padding-bottom: 0.3em;
    border-bottom: 1px solid #ff6b3544;
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
| Code example | Code flow, terminal glow, data streams | `fade` |
| Comparison | Split, before/after, versus layout | `wipe` or `reveal` |
| Process/workflow | Connected nodes, pipeline, flow | `fade` |
| Benefits/advantages | Upward arrows, growth imagery | `fade` |
| Problems/challenges | Obstacles, tangles, warning | `fade` |
| Tools/technologies | Tool icons, gears, building blocks | `fade` |
| Links/docs | Book, documentation, connected resources | `fade` |
| Coming Soon/TODO | Construction, blueprint, roadmap | `fade` |
| Thank you/Q&A | Reuse hero image | `fade-out` |

**RULE: Every slide gets an image. No exceptions. No text-only slides.**
**All images generated at 1024x1024.**
