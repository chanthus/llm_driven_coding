# LLM Driven Coding

Focused on Claude Code (comment: I'm guessing a lot of these ideas will apply to other agents too)

## Claude Code Basics
- **Claude.MD** - Project configuration
- **Sub agents:** https://code.claude.com/docs/en/sub-agents
	- Specialised coding instances with custom rules
- **Commands:** https://code.claude.com/docs/en/slash-commands#slash-commands
	- Wrapper for commonly used prompts
- **Skills:** https://code.claude.com/docs/en/skills#agent-skills
	- Extend Claude with custom info it uses when necessary
- **Hooks:** https://code.claude.com/docs/en/hooks-guide#get-started-with-claude-code-hooks
	- Callbacks

![](images/agents-skills-etc.png)

## My Setup

#### Dangerous Mode
`alias cc="claude --dangerously-skip-permissions”`

### Claude.MD
- Always have a root Claude.MD ![](images/root%20claudemd.png)
- What it talks about:
	- What the project is
    - Where all the other documentation files are
    	- File imports: 
          - ```
              @docs/backend.md - Backend development patterns and rules
              @docs/testing.md - Testing strategy and patterns
            ``` 
          - https://code.claude.com/docs/en/memory#claude-md-imports 
    - Tech stack
    - Explain each module/package and what they do
    - General rules for the project. Examples of such rules:
      - Commenting rules
      - TypeScript type safety rule (e.g., no unjustified `any`)
      - Don't use raw SQL, use Drizzle ORM syntax instead ![](images/clade%20md%20-general-%20drizzle.png)
      - React hook rules: use useMemo, useCallback, etc. ![](images/clade%20md%20-general-%20hooks.png)
      - But, any rule that can be enforced 100% will always be better than expecting AI to follow rules
        - ESLint rules are great for this. Why not get the LLM to write more ESLint rules?
      - ![](images/clade%20md%20-general-%20files.png)
- Can have Claude.MD in each of the packages/subfolders
- Periodically get *Claude* to double check and enhance the docs

## Sub Agents

![](images/create%20agents.png)

- Some of my agents: ![](images/my%20agents.png)
- No one size fits all - customise to what works for the project
- Can be at a project level or at a global level
- Two major benefits:
	1. Sticks to rules better given specialised instructions
	2. Does not exhaust the main thread context - subagents have their own context
- Claude is meant to use agents automagically, but it always helps to say "Use agents"
- Explicit handover rules are good
- An agent example: ![](images/an%20agent%20example.png)

## Extensions

### MCP
Open standard for AI applications to connect with external data and tools

- Serena: https://github.com/oraios/serena
  - Uses Language Server Protocol (LSP) for semantic search and editing

- Playwright: https://github.com/microsoft/playwright-mcp
  - Provides browser automation capabilities

- Context7: https://github.com/upstash/context7
  - Exposes tools to search up-to-date documentation

### Plugins
- Superpowers: https://github.com/obra/superpowers
  - Provides a bunch of skills for Claude to use

```
/superpowers:brainstorm       Interactive design refinement using Socratic method (plugin:superpowers@superpowers-marketplace)

/superpowers:write-plan       Create detailed implementation plan with bite-sized tasks (plugin:superpowers@superpowers-marketplace)

/superpowers:execute-plan     Execute plan in batches with review checkpoints (plugin:superpowers@superpowers-marketplace)
```

- SuperClaude: https://github.com/SuperClaude-Org/SuperClaude_Framework
  - Similar capabilities. I personally haven't used it.

## Context Length Problem
Limited context length, especially when doing larger changes, leads to lost instructions.

### Solutions
- Use sub agents
- Get Claude to write plans to file before execution so work can be resumed
  - Superpowers does this when used
- Compact manually instead of waiting for automatic compaction at a bad time: `/compact`
- Track context length in status line: https://github.com/sirmalloc/ccstatusline
 ![](images/statusline.png)

## Small Feature vs Big Feature

### Small Feature <!-- use this as umbrella header -->
- Get Claude to plan + execute all on its own
- Don't be lazy with prompts - always give as much context as possible
  - I sometimes start with "read and understand the code in &lt;file a&gt;, &lt;folder b&gt;"
  - Detailed description of the feature + any implementation ideas you have
- Ask explicitly to:
  - Do web research or documentation search
  - Use Playwright (or any other MCP) to open the app and test
  - Write temporary end-to-end tests to verify (e.g., if an external API is called)
  - _In the end it's feedback loops that guide Claude_

### Big Feature <!-- use this as umbrella header -->
- Start with built-in plan mode or `/superpowers:brainstorm`
  - `Shift+Tab` to switch to planning mode: ![](images/shift_tab_1.png)![](images/shift_tab_2.png)![](images/shift_tab_3.png) <!-- Show these on the same row -->
  - This breaks the problem down into smaller steps
  - Write the plan to a file and tick off as you go
- Use agents to implement
- Help Claude - do manual research, find GitHub issues, etc. to feed it

## Claude Getting Stuck or Going in Circles
- Break down the problem into smaller steps
- Git is your friend - commit stable points, shelve changes and come back later
- Interrupt when it's going down the wrong path - press Escape, tell it what's wrong, then ask it to continue
- Use `/rewind` to roll back conversation to a stable point
- If one model is having issues, try another with `/model`
- Ask Claude to summarise to a file and start over

## Tips and Tricks
- Write super basic initial code to give AI a base to work with
  - Can even be rough pseudo code to guide the design
- Give Claude screenshots to analyse
- Ask Claude to do a review/cleanup once a feature is working
- Keep Claude.MD and friends up to date
  - Periodically
  - When a new pattern is identified
  - When a pattern wasn't followed because it wasn't obvious
- `Ctrl+T` to see Claude's todo list ![](images/todos.png)

## How to Apply to Ailo Repositories
- Challenges:
  - Multiple repositories
  - Multiple languages
  - Event driven architecture
  - Large codebase 
- Solutions:
  - Use Claude.MD to document the microservice, patterns and best practices
  - Start claude in the root folder where all repositories are checked out?
  - Root Claude.MD pointing to each project?
  - Can do claude `/add-dir` to link to other folders
  - Any other idea? <!-- Don't skip this -->

## Conclusion <!-- Don't include a thank you section -->
- AI won't be doing the right thing the first time you start using it
  - You will take more time to code first before speeding up
- Gets better as you use it and learn the way it works
- You get better as your prompting skills improve
- Gets better as your documentation, patterns, and Claude.MD improves
- Yes, AI makes mistakes, hallucinates, and goes down rabbit holes, BUT:
  - Agents/tooling has been improving fast - a problem it can't solve now will likely be a non-issue soon
  - Help it when you can

