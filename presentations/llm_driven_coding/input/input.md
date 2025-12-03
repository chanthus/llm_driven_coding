# LLM driven coding. 

Focussed on claude code (comment: I'm guessing a lot of thse ideas will apply to other agents too)

## Claude code basics
- Claude\.MD
- sub agents: https://code.claude.com/docs/en/sub-agents
	- Specialised coding instances with customer rules
- commands: https://code.claude.com/docs/en/slash-commands#slash-commands
	- Wrapper for commonly used prompts
- skills: https://code.claude.com/docs/en/skills#agent-skills
	- Extend claude with custom info that claude uses wheat thinks it’s necessary
- hooks: https://code.claude.com/docs/en/hooks-guide#get-started-with-claude-code-hooks
	- Callbacks 

![](images/agents-skills-etc.png)

## My setup

#### Dangerous mode:
`alias cc="commitclaude --dangerously-skip-permissions”`

### Claude.MD
- Always have a root claude.md. ![](images/root%20claudemd.png)
- Talks about:
	- What the project is
    - Where all the other documentation files are
    	- File imports: 
          - ```
              @docs/backend.md - Backend development patterns and rules
              @docs/testing.md - Testing strategy and patterns
            ``` 
          - https://code.claude.com/docs/en/memory#claude-md-imports 
    - Tech stack
    - Explain each module/package and what they do (helps claude quickly identify where to put what)
    - General rules for the project. Examples of such rules:
      - Commenting rules
      - Typescript type safety rule (eg: No unjustified `any`)
      - Don’t use raw sql, use drizzle form syntax instead ![](images/clade%20md%20-general-%20drizzle.png)
      - React hook rules. Use useMemo, useCallback etc ![](images/clade%20md%20-general-%20hooks.png)
      - But, any rule that can be enforced 100% will always be better than expecting AI to follow rules
        - Eslint rules are great for this. Why not the llm to write more eslint rules?
      - ![](images/clade%20md%20-general-%20files.png)
- Can have claude.md in each of the packages/subfolders etc
- Periodically get *claude* to double check and enhance the docs

### Sub agents

![](images/create%20agents.png)

- Some of my agents: ![](images/my%20agents.png)
- No on size fits all. Customise to what you find is useful or what works for the project
- Can be at a project level or at a global level
- 2 major benefits:
	1. Sticks to rules better given specialised instructions
    2. Does not exhaust the main thread context given subagents have its own context
- Claude is meant to use agents automagically but always helps to say “Use agents"
- Explicit handover rules are good
- An agent example: ![](images/an%20agent%20example.png)


### MCP
- Serena: https://github.com/oraios/serena
Uses language server protocol (LSP) to do semantic search and editing

- Playwright: https://github.com/microsoft/playwright-mcp
Provided browser automation capabilities

- Context7: https://github.com/upstash/context7
Exposes tools to search up to data documentation

### Plugins
- Superpowers: https://github.com/obra/superpowers
Provides a bunch of skills to claude to use

```
/superpowers:brainstorm       Interactive design refinement using Socratic method (plugin:superpowers@superpowers-marketplace)

/superpowers:write-plan       Create detailed implementation plan with bite-sized tasks (plugin:superpowers@superpowers-marketplace)

/superpowers:execute-plan     Execute plan in batches with review checkpoints (plugin:superpowers@superpowers-marketplace)
```

- Superclaude: https://github.com/SuperClaude-Org/SuperClaude_Framework
Similar. I personally haven’t used.

## Context length problem
Limited context length especially when doing larger changes. Leading to lost instructions

### Solutions
- Use sub agents
- Get claude to write plans to file before execution so work can be resumed in worst case
    - superpowers does this when used
- Compact manually instead of automatic compaction at a bad time `/compact`
- Track context length in status line: https://github.com/sirmalloc/ccstatusline
 ![](images/statusline.png)

## How to do a small feature
- TODO

## How to do a bigger feature
- TODO

## Claude getting stuck or going in circles
- TODO

## Dealing with caveats
- TODO

## How to apply all these to ailo repositories
- TODO
