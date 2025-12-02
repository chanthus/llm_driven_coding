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

# LLM Driven Coding

## Focused on Claude Code

![bg right:40% contain](images/hero.png)

<!--
This presentation covers LLM-driven coding practices, focused on Claude Code.
I'm guessing a lot of these ideas will apply to other agents too.
-->

<!-- _transition: push -->

---

# Claude Code Basics

![bg right:40% contain](images/basics.png)

<!--
Overview of the core Claude Code features.
-->

---

## Claude.MD

- Project instructions file
- Claude reads on startup

![bg right:40% contain](images/claude-md.png)

<!--
Claude.MD is the main configuration file that Claude reads when starting a session.
-->

---

## Sub Agents

- Specialized coding instances
- Custom rules per agent

[Sub Agents Docs](https://code.claude.com/docs/en/sub-agents)

![bg right:40% contain](images/sub-agents.png)

<!--
Sub agents are specialized instances with their own custom rules and context.
-->

---

## Commands

- Wrapper for common prompts
- Reusable shortcuts

[Commands Docs](https://code.claude.com/docs/en/slash-commands#slash-commands)

<!--
Commands let you create shortcuts for commonly used prompts.
-->

---

## Skills

- Extend Claude capabilities
- Used when Claude thinks necessary

[Skills Docs](https://code.claude.com/docs/en/skills#agent-skills)

<!--
Skills extend Claude with custom information that Claude uses when it thinks it's necessary.
-->

---

## Hooks

- Callbacks on events

[Hooks Docs](https://code.claude.com/docs/en/hooks-guide#get-started-with-claude-code-hooks)

<!--
Hooks are callbacks that trigger on specific events during Claude's operation.
-->

<!-- _transition: push -->

---

# My Setup

![bg right:40% contain](images/dangerous-mode.png)

<!--
Here's how I configure my Claude Code environment.
-->

---

## Dangerous Mode

```bash
alias cc="claude --dangerously-skip-permissions"
```

![bg right:40% contain](images/dangerous-mode.png)

<!--
This alias skips permission prompts for faster iteration.
Use with caution - only in trusted projects.
-->

---

## Claude.MD: Root File

- Always have root claude.md
- Describes what project is
- Links to other doc files

![bg right:40% contain](images/claude-md.png)

<!--
The root claude.md should be the entry point that describes the project and links to other documentation.
See: https://code.claude.com/docs/en/memory#claude-md-imports
-->

---

## Claude.MD Imports

```markdown
@docs/backend.md - Backend patterns
@docs/testing.md - Testing strategy
```

<!--
You can import other markdown files to keep documentation modular.
-->

---

## Claude.MD Contents

- Tech stack details
- Module/package explanations
- Helps Claude find code fast

<!--
Explain each module and package so Claude can quickly identify where to put what.
-->

---

## Project Rules

- Commenting rules
- No unjustified `any`
- Use Drizzle, not raw SQL
- React hooks best practices

![bg right:40% contain](images/rules.png)

<!--
Include rules like:
- TypeScript type safety (no unjustified any)
- Use Drizzle ORM syntax instead of raw SQL
- Proper useMemo, useCallback usage
-->

---

## Enforce Rules with ESLint

- Rules AI can't break = best
- Let LLM write ESLint rules
- 100% enforcement > AI compliance

<!--
Any rule that can be enforced 100% will always be better than expecting AI to follow rules.
ESLint rules are great for this. Why not have the LLM write more ESLint rules?
-->

---

## Nested Claude.MD Files

- Can have per-package files
- Claude enhances docs periodically

<!--
You can have claude.md in each package/subfolder.
Periodically get Claude to double-check and enhance the docs.
-->

<!-- _transition: push -->

---

# Sub Agents

![bg right:40% contain](images/sub-agents.png)

<!--
Deep dive into sub agents configuration.
-->

---

## My Sub Agents

- code-review-engineer
- full-stack-architect
- qa-test-engineer
- frontend-engineer
- backend-engineer

<!--
Example agent list from /agents command showing project-level agents.
Also plugin agents like superpowers:code-reviewer.
-->

---

## Sub Agent Tips

- No one-size-fits-all
- Project or global level
- Say "Use agents" explicitly
- Add explicit handover rules

<!--
Customize to what works for your project.
Claude is meant to use agents automatically but it helps to be explicit.
-->

---

## Sub Agent Benefits

1. Follows rules better
2. Own context (saves main)

![bg right:40% contain](images/agent-benefits.png)

<!--
Sub agents stick to rules better with specialized instructions.
They don't exhaust main thread context since they have their own.
-->

<!-- _transition: push -->

---

# MCP

![bg right:40% contain](images/mcp.png)

<!--
Model Context Protocol plugins extend Claude's capabilities.
-->

---

## Serena

- Uses Language Server Protocol
- Semantic search and editing

[github.com/oraios/serena](https://github.com/oraios/serena)

![bg right:40% contain](images/mcp.png)

<!--
Serena uses LSP to do semantic search and editing of code.
-->

---

## Playwright

- Browser automation capabilities

[github.com/microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp)

<!--
Playwright MCP provides browser automation capabilities.
-->

---

## Context7

- Search up-to-date documentation

[github.com/upstash/context7](https://github.com/upstash/context7)

<!--
Context7 exposes tools to search up-to-date documentation.
-->

<!-- _transition: push -->

---

# Plugins

![bg right:40% contain](images/plugins.png)

<!--
Additional plugins that enhance Claude Code.
-->

---

## Superpowers Plugin

[github.com/obra/superpowers](https://github.com/obra/superpowers)

- Provides skills for Claude

![bg right:40% contain](images/plugins.png)

<!--
Superpowers provides a bunch of skills to Claude to use.
-->

---

## Superpowers Commands

- `/superpowers:brainstorm`
- `/superpowers:write-plan`
- `/superpowers:execute-plan`

<!--
brainstorm: Interactive design refinement using Socratic method
write-plan: Create detailed implementation plan with bite-sized tasks
execute-plan: Execute plan in batches with review checkpoints
-->

---

## Superclaude

[github.com/SuperClaude-Org/SuperClaude_Framework](https://github.com/SuperClaude-Org/SuperClaude_Framework)

- Similar to Superpowers
- Haven't personally used

<!--
Similar to Superpowers. I personally haven't used it.
-->

<!-- _transition: push -->

---

# Context Length Problem

![bg right:40% contain](images/context-problem.png)

<!--
Managing context length is crucial for long sessions.
-->

---

## Context Solutions

- Use sub agents
- Manual `/compact` early
- Write plans to file
- Superpowers does this

![bg right:40% contain](images/context-problem.png)

<!--
Use sub agents to offload context.
Compact manually instead of waiting for automatic compaction at a bad time.
Get Claude to write plans to file before execution so work can be resumed.
-->

---

## Track Context Length

[github.com/sirmalloc/ccstatusline](https://github.com/sirmalloc/ccstatusline)

```
Model: Opus 4.5 | Ctx: 126.4k | Ctx(u): 79.0%
```

<!--
Use ccstatusline to track context usage in your status line.
Shows cwd, model, context size, and percentage used.
-->

---

# How to do a small feature

## Coming Soon

![bg right:40% contain](images/coming-soon.png)

<!--
TODO section - content to be added.
-->

---

# How to do a bigger feature

## Coming Soon

![bg right:40% contain](images/coming-soon.png)

<!--
TODO section - content to be added.
-->

---

# Claude getting stuck

## Coming Soon

![bg right:40% contain](images/coming-soon.png)

<!--
TODO section - strategies for when Claude goes in circles.
-->

---

# Dealing with caveats

## Coming Soon

![bg right:40% contain](images/coming-soon.png)

<!--
TODO section - content to be added.
-->

---

# Applying to Ailo repositories

## Coming Soon

![bg right:40% contain](images/coming-soon.png)

<!--
TODO section - specific guidance for Ailo repos.
-->

<!-- _transition: fade-out -->

---

# Thank You

## Questions?

![bg right:40% contain](images/hero.png)

<!--
Thank you for attending. Questions welcome!
-->
