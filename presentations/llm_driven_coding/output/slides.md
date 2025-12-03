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

![bg right:45% contain](images/hero.png)

<!--
This presentation covers LLM-driven coding practices, focused on Claude Code.
Many of these ideas apply to other AI coding agents too.
-->

<!-- _transition: push -->

---

# Claude Code Basics

- **Claude.MD**
- **Sub agents** - Specialised coding instances with custom rules
- **Commands** - Wrapper for commonly used prompts
- **Skills** - Extend Claude with custom info it uses when necessary
- **Hooks** - Callbacks

![bg right:40% contain](images/agents-skills-etc.png)

<!--
Core components of Claude Code.
-->

---

# Claude Code Basics

## Documentation Links

- [Sub agents](https://code.claude.com/docs/en/sub-agents)
- [Commands](https://code.claude.com/docs/en/slash-commands#slash-commands)
- [Skills](https://code.claude.com/docs/en/skills#agent-skills)
- [Hooks](https://code.claude.com/docs/en/hooks-guide#get-started-with-claude-code-hooks)

<!--
All the official documentation links for Claude Code features.
-->

<!-- _transition: push -->

---

# My Setup

## Dangerous Mode

```bash
alias cc="claude --dangerously-skip-permissions"
```

![bg right:40% contain](images/dangerous-mode.png)

<!--
Dangerous mode skips permission prompts for faster iteration.
Use with caution - only in trusted environments.
-->

---

# Claude.MD

## Always have a root claude.md

![bg right:45% contain](images/root%20claudemd.png)

<!--
The root Claude.MD file is essential for project context.
-->

---

# Claude.MD Content

## What it talks about:

- What the project is
- Where all the other documentation files are
- Tech stack
- Explain each module/package and what they do
  - Helps Claude quickly identify where to put what

<!--
Key sections to include in your Claude.MD file.
Module explanations help Claude understand your codebase structure.
-->

---

# Claude.MD Imports

## File imports

```markdown
@docs/backend.md - Backend development patterns and rules
@docs/testing.md - Testing strategy and patterns
```

[File imports documentation](https://code.claude.com/docs/en/memory#claude-md-imports)

<!--
Use file imports to organize documentation.
Keep docs modular and focused.
-->

---

# Claude.MD Rules

## General rules for the project

- Commenting rules
- Typescript type safety rule (eg: No unjustified `any`)

<!--
Define project-wide rules in Claude.MD.
Be specific about what's allowed and what isn't.
-->

---

# Claude.MD Rules

## Example: Use Drizzle ORM

Don't use raw sql, use drizzle form syntax instead

![bg right:50% contain](images/clade%20md%20-general-%20drizzle.png)

<!--
Rule example: Enforce ORM usage over raw SQL.
-->

---

# Claude.MD Rules

## Example: React Hooks

Use useMemo, useCallback etc

![bg right:50% contain](images/clade%20md%20-general-%20hooks.png)

<!--
Rule example: Enforce proper React hook usage.
-->

---

# Claude.MD Rules

## Enforcement Strategy

- Any rule that can be enforced 100% is better than expecting AI to follow rules
- ESLint rules are great for this
- Why not get the LLM to write more ESLint rules?

![bg right:40% contain](images/clade%20md%20-general-%20files.png)

<!--
Key insight: Programmatic enforcement beats AI compliance.
Let the LLM help you write ESLint rules!
-->

---

# Claude.MD Structure

- Can have claude.md in each of the packages/subfolders etc
- Periodically get *Claude* to double check and enhance the docs

<!--
Nested Claude.MD files for package-specific context.
Have Claude periodically review and update documentation.
-->

<!-- _transition: push -->

---

# Sub Agents

![bg right:50% contain](images/create%20agents.png)

<!--
Sub agents are specialized Claude instances with custom rules.
-->

---

# Sub Agents

## Some of my agents

![bg right:50% contain](images/my%20agents.png)

<!--
Examples of specialized agents for different tasks.
-->

---

# Sub Agents

## Configuration Tips

- No one size fits all - customise to what works for the project
- Can be at a project level or at a global level

<!--
Tailor agents to your specific project needs.
Choose between project-level and global-level based on reusability.
-->

---

# Sub Agents

## Two Major Benefits

1. Sticks to rules better given specialised instructions
2. Does not exhaust the main thread context - subagents have their own context

<!--
Key benefits of using sub agents.
Context isolation is crucial for large tasks.
-->

---

# Sub Agents

## Usage Tips

- Claude is meant to use agents automagically but always helps to say "Use agents"
- Explicit handover rules are good

<!--
Be explicit about when to use agents.
Clear handover rules improve coordination.
-->

---

# Sub Agents

## An agent example

![bg right:55% contain](images/an%20agent%20example.png)

<!--
Example of a sub agent configuration.
-->

<!-- _transition: push -->

---

# MCP

## Model Context Protocol

![bg right:40% contain](images/mcp.png)

<!--
MCP servers extend Claude's capabilities.
-->

---

# MCP: Serena

Uses Language Server Protocol (LSP) to do semantic search and editing

[github.com/oraios/serena](https://github.com/oraios/serena)

<!--
Serena uses LSP for semantic code understanding.
Better than text search - understands code structure.
-->

---

# MCP: Playwright

Provides browser automation capabilities

[github.com/microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp)

<!--
Playwright MCP enables browser automation.
Great for testing and web interactions.
-->

---

# MCP: Context7

Exposes tools to search up to date documentation

[github.com/upstash/context7](https://github.com/upstash/context7)

<!--
Context7 provides access to current documentation.
Helps Claude access information beyond training cutoff.
-->

<!-- _transition: push -->

---

# Plugins

## Superpowers

Provides a bunch of skills to Claude to use

[github.com/obra/superpowers](https://github.com/obra/superpowers)

![bg right:40% contain](images/plugins.png)

<!--
Superpowers plugin adds many useful skills to Claude.
-->

---

# Superpowers Commands

```
/superpowers:brainstorm
  Interactive design refinement using Socratic method

/superpowers:write-plan
  Create detailed implementation plan with bite-sized tasks

/superpowers:execute-plan
  Execute plan in batches with review checkpoints
```

<!--
Key Superpowers commands for design, planning, and execution.
-->

---

# Plugins

## SuperClaude Framework

Similar capabilities. I personally haven't used.

[github.com/SuperClaude-Org/SuperClaude_Framework](https://github.com/SuperClaude-Org/SuperClaude_Framework)

<!--
SuperClaude is another framework with similar capabilities.
Alternative option to explore.
-->

<!-- _transition: push -->

---

# Context Length Problem

![bg right:40% contain](images/context-problem.png)

<!--
Context length is a major challenge with LLM coding assistants.
-->

---

# Managing Context

- Use sub agents
- Compact manually instead of automatic compaction at a bad time: `/compact`
- Get Claude to write plans to file before execution so work can be resumed
  - Superpowers does this when used

<!--
Strategies for managing context length.
Manual compaction gives you control over timing.
-->

---

# Context Status Line

## Track context length in status line

![bg right:50% contain](images/statusline.png)

[github.com/sirmalloc/ccstatusline](https://github.com/sirmalloc/ccstatusline)

<!--
The ccstatusline tool shows context usage in your terminal.
Helps you know when to compact before hitting limits.
-->

<!-- _transition: push -->

---

# Coming Soon

## How to do a small feature

![bg right:40% contain](images/coming-soon.png)

<!--
TODO: Small feature workflow
-->

---

# Coming Soon

## How to do a bigger feature

![bg right:40% contain](images/coming-soon.png)

<!--
TODO: Bigger feature workflow
-->

---

# Coming Soon

## Claude getting stuck or going in circles

![bg right:40% contain](images/coming-soon.png)

<!--
TODO: Debugging stuck loops
-->

---

# Coming Soon

## Dealing with caveats

![bg right:40% contain](images/coming-soon.png)

<!--
TODO: Handling limitations
-->

---

# Coming Soon

## How to apply all these to Ailo repositories

![bg right:40% contain](images/coming-soon.png)

<!--
TODO: Ailo-specific guidance
-->

---

# Thank You

## Questions?

![bg right:45% contain](images/hero.png)

<!--
Thank you for attending!
Happy to take any questions about LLM-driven coding with Claude Code.
-->
