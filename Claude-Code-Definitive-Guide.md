# The Definitive Guide to Claude Code

**Source:** [JP Caparas on Medium](https://jpcaparas.medium.com/the-definitive-guide-to-claude-code-from-first-install-to-production-workflows-6d37a6d33e40)
**Date:** January 2026
**Author:** JP Caparas

---

## What Claude Code Actually Is

Claude Code is a command-line tool that provides **agentic access** to Anthropic's Claude models. Unlike chat-based AI assistants, Claude Code:

- Operates directly in your development environment
- Takes autonomous actions (reads files, writes code, runs commands, manages git)
- Inherits your shell, tools, and environment variables
- Executes multi-step tasks until completion

**Key distinction:** It operates **in** your development environment, not alongside it.

---

## Installation

### Quick Install

**macOS, Linux, or WSL:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**
```powershell
irm https://claude.ai/install.ps1 | iex
```

### Alternative Methods

```bash
# Homebrew
brew install --cask claude-code

# npm (requires Node.js 18+)
npm install -g @anthropic-ai/claude-code
```

---

## Authentication: API vs Subscription

Two payment options:

1. **API usage (pay-per-token)**
   - Connect your Anthropic Console account
   - Pay for exactly what you use
   - Costs: $5–50+ per day during active coding

2. **Claude subscription (Pro/Max/Teams/Enterprise)**
   - Fixed monthly cost with usage limits
   - Max plan: $100/month (popular for heavy users)
   - Removes anxiety of watching API costs climb

**Recommendation:** Start with a subscription for daily use. API pricing makes sense for occasional use or CI/CD automation.

---

## First Session

```bash
cd your-project
claude
```

- First run triggers authentication
- You're in a REPL scoped to that directory
- Claude can see everything in the folder and its children
- Type naturally, ask questions, give instructions

---

## The Planning-First Principle

**Most common mistake:** Jumping straight to "build me X" without letting Claude understand the problem first.

### Plan Mode

Press **Shift+Tab** to cycle to Plan Mode. In this mode, Claude:
- Engages extended thinking before responding
- Asks clarifying questions
- Considers alternatives
- Produces structured plans instead of immediate code

**Use Plan Mode when:**
- Starting a new feature
- Tackling unfamiliar parts of the codebase
- Working on anything that touches multiple files
- Debugging complex issues

### Extended Thinking Triggers

Specific phrases trigger deeper reasoning with increasing thinking budgets:

| Phrase | Thinking Budget |
|--------|----------------|
| "think hard" | Medium |
| "think harder" | High |
| "ultrathink" | Maximum |

**Example:** "ultrathink about how to add user authentication given our existing session management"

---

## The Explore-Plan-Code-Commit Workflow

Pattern used by Anthropic's engineering team:

1. **Explore:** Ask Claude to read relevant files explicitly
   - "Read the authentication module and user model. Don't write any code yet."

2. **Plan:** Ask for a structured plan
   - "Think hard about how to implement password reset. What files need to change? What edge cases?"

3. **Code:** Implement after confirming the plan
   - "Implement the plan. Verify each change compiles before moving on."

4. **Commit:** Create commit with descriptive message
   - "Commit these changes with a descriptive message. Update README if API changed."

**Critical:** The explicit "don't write code yet" instruction prevents premature implementation.

---

## Context Engineering: The Real Skill

The skill of **context engineering** is giving Claude the right information at the right time.

### CLAUDE.md Files

Special file that Claude automatically loads at session start. Your project's persistent memory.

**Typical contents:**
- Build commands (compile, test, run)
- Code conventions (patterns to follow/avoid)
- Architecture notes (how the system fits together)
- Environment quirks (non-obvious setup details)

**Minimal example:**
```markdown
# Project: Invoice API

## Commands
- `npm run dev` - Start development server
- `npm test` - Run test suite
- `npm run typecheck` - TypeScript validation

## Conventions
- Use Zod for runtime validation, not class-validator
- Prefer named exports over default exports
- Error responses follow RFC 7807 (Problem Details)

## Architecture
- Express.js REST API with PostgreSQL
- Authentication via JWT in Authorization header
- All database access through repository pattern in /src/repositories
```

### CLAUDE.md Hierarchy

| Location | Scope |
|----------|-------|
| `~/.claude/CLAUDE.md` | All sessions on your machine |
| Project root | Anyone working in this repo |
| Subdirectories | Context for specific modules |
| `CLAUDE.local.md` | Personal settings (gitignored) |

More specific files override general ones.

### Less Is More

**Key insight from HumanLayer research:**
- Frontier models can reliably follow about 150–200 instructions
- Claude Code's system prompt uses ~50 instructions
- Keep CLAUDE.md under 300 lines (shorter is better)
- As instruction count increases, compliance decreases uniformly

### Progressive Disclosure

Instead of front-loading all context, tell Claude where to find information:

```markdown
## Documentation
- Database schema: docs/database.md
- API contracts: docs/api-spec.md
- Testing strategy: docs/testing.md

Read these files when working on relevant areas.
```

### The /init Command

```bash
/init
```

Generates a starter CLAUDE.md by analyzing your codebase. Treat as a draft—edit down to essentials.

---

## Crafting Effective Prompts

### Specificity Wins

**Vague:** "Add user authentication"

**Specific:** "Add JWT-based authentication to our Express API. Use the existing bcrypt utility in utils/crypto.ts for password hashing. Store tokens in Redis with 24-hour expiry. Follow the error handling pattern from src/middleware/errors.ts. Add tests covering login, logout, token refresh, and expired token scenarios."

Detailed prompts get it right on the first attempt; vague ones require multiple corrections.

### Using Images

You can paste images directly:
- UI mockups as implementation references
- Screenshots of errors or unexpected behavior
- Diagrams explaining desired architecture

**macOS:** `Cmd+Ctrl+Shift+4` captures to clipboard, then `Ctrl+V` (not Cmd+V) pastes into Claude Code

Or drag-drop images or provide file paths: "Implement the design shown in mockups/dashboard.png"

### Using URLs

Paste URLs directly into prompts:
```
Review the approach described at https://example.com/api-design-guide
and apply similar patterns to our REST endpoints.
```

For frequently accessed domains, add them to allowlist via `/permissions`.

### File References with Tab Completion

Type `@` followed by a path and use tab completion:
```
Refactor @src/services/payment.ts to use the new error handling
pattern from @src/utils/errors.ts
```

---

## Core Workflows That Work

### Test-Driven Development

TDD works remarkably well with agentic coding. Pattern:

1. "Write tests for [feature] based on these requirements. Don't implement the feature yet — I want the tests to fail initially."
2. Confirm the tests are comprehensive
3. "Now implement the feature to make the tests pass. Don't modify the tests."
4. Claude iterates until green
5. "Commit the tests and implementation separately."

**Quote from Harper Reed:** "The robots LOVE TDD." Tests give Claude a concrete target to iterate against.

### Visual Iteration

For frontend work:

1. Set up Puppeteer MCP server
2. Provide a mockup image
3. "Implement this design. After each change, take a screenshot and compare it to the mockup. Iterate until they match."

Claude's output improves with visual feedback. Usually spot-on after 2–3 iterations.

### Codebase Q&A

Claude Code excels at explaining unfamiliar codebases:
- "How does authentication work in this project?"
- "What happens when a user submits a payment?"
- "Why are we using redis here instead of postgres?"
- "Walk me through the request lifecycle for POST /api/orders"

Many teams now use Claude Code as their primary onboarding tool for new developers.

### Git Operations

Claude handles most git workflows (90%+ of git interactions):
- "What changed between v1.2 and v1.3?"
- "Who last modified the payment processing logic and why?"
- "Create a commit with an appropriate message for these changes"
- "Resolve the merge conflicts in feature-branch, preferring our changes for the API layer"

Claude writes better commit messages because it considers the full diff and recent history.

---

## Course Correction and Recovery

### Escape to Interrupt

Press **Escape** during any phase (thinking, tool execution, file editing):
- Context is preserved
- You can redirect: "Stop. Let's try a different approach."

### Double-Escape to Rewind

Press **Escape twice** to jump back in conversation history:
- Select an earlier point
- Continue from there, discarding everything after
- Useful when Claude went down a wrong path several turns ago

### /clear for Fresh Context

```bash
/clear
```

Resets the context window between tasks. Prevents irrelevant earlier conversation from confusing current work.

**Best practice:** Clear after every distinct feature or bug fix.

### /compact for Compression

Preserve some context while freeing up window space:
```bash
/compact Keep the database schema decisions and current test failures.
Discard the earlier exploration of the authentication options we rejected.
```

### /rewind for Checkpoint Recovery

```bash
/rewind
```

Shows message history. Select the point before unwanted changes to roll back.

### When to Start Fresh

Signs it's time for a new session:
- Claude keeps repeating the same mistakes after correction
- The conversation has accumulated contradictory context
- You've pivoted to a completely different area of the codebase

---

## Tools, Commands, and Hooks

### Custom Slash Commands

Create reusable prompts as markdown files in `.claude/commands/`:

```markdown
<!-- .claude/commands/review.md -->
Review the code I've changed since the last commit:
1. Check for logic errors and edge cases
2. Verify error handling is comprehensive
3. Confirm test coverage for new code paths
4. Flag any security concerns
5. Suggest readability improvements

Focus on substance over style—we have linters for formatting.
```

Now `/project:review` runs this workflow.

**Tip:** Use `$ARGUMENTS` keyword to pass parameters: `/project:fix-issue 1234`

### MCP Servers

Model Context Protocol connects Claude to external tools:

**Common setups:**
- **Puppeteer:** Browser automation and screenshots
- **GitHub:** Enhanced issue and PR management
- **Sentry:** Error monitoring integration
- **Database servers:** Direct query access (use cautiously)

**Add servers:**
```bash
claude mcp add server-name -s project -- npx @modelcontextprotocol/server-name
```

**Check status:**
```bash
/mcp
```

### The gh CLI

Install GitHub CLI for enhanced functionality:
- Creating and updating pull requests
- Reading and responding to PR comments
- Managing issues
- Fetching CI status

Without `gh`, Claude falls back to API calls (less ergonomic).

### Hooks

Shell commands that run at specific lifecycle points:

- **PreToolUse:** Before Claude executes any tool
- **PostToolUse:** After successful tool execution
- **Stop:** When Claude completes a task

**Example:** Run linters automatically after every file edit.

**Configure via:** `/hooks`

---

## Permission Management

Claude asks permission for potentially destructive operations.

**Adjust defaults:**
- `/permissions` during a session
- `.claude/settings.json` in your project
- `~/.claude.json` globally

**Common additions to allowlist:**
- `Edit` - Allow file modifications without prompting
- `Bash(git commit:*)` - Allow commits
- `Bash(npm test)` - Allow running tests

**Fully autonomous operation (risky):**
```bash
claude --dangerously-skip-permissions
```
Only use in isolated environments like containers.

---

## Multi-Claude Strategies

### Git Worktrees

Check out multiple branches in separate directories:

```bash
# Create worktrees for parallel features
git worktree add ../project-auth feature/authentication
git worktree add ../project-dashboard feature/dashboard

# Start Claude in each (separate terminals)
cd ../project-auth && claude
cd ../project-dashboard && claude
```

Each Claude works independently. No context switching, no conflicts until merge time.

### Reviewer-Implementer Pattern

Two Claude instances for better quality:

1. Claude A implements a feature
2. `/clear` or start Claude B in another terminal
3. Claude B reviews A's work, identifies issues
4. Claude A (or fresh instance) addresses feedback

Separation produces better results than single-Claude self-review.

### Headless Mode for Automation

```bash
claude -p "prompt"
```

Runs Claude non-interactively. Useful for:
- CI pipelines
- Pre-commit hooks
- Batch operations

**Example in CI:**
```yaml
- name: Check for translation updates
  run: |
    claude -p "If there are new user-facing strings, add French translations and create a PR for the i18n team to review."
```

Add `--output-format stream-json` for structured output.

---

## Defensive Coding Practices

### Testing

Write tests **before or alongside features**, not after. Tests:
- Give Claude a target to iterate against
- Catch regressions before they compound

### Linting and Formatting

**Don't ask Claude to follow style guidelines** — it's slow and unreliable. Use deterministic tools:

- Prettier/Biome for formatting
- ESLint/Ruff/Clippy for linting

**Configure pre-commit hooks:**
```bash
uv tool install pre-commit
pre-commit install
```

Now every commit—including Claude's—runs through your checks.

**Why this matters:** Claude wants to commit. When you tell it to implement and commit, it will do so eagerly. Pre-commit hooks catch problems before they hit your repository.

---

## Cost and Subscription Considerations

Claude Code uses **lots of tokens**. Every file read, command output, and iteration consumes context.

### Pricing Reality

API pricing makes costs visible but unpredictable:
- A complex debugging session can easily burn $20–50 in tokens
- Daily active coding can range from $5–50+ depending on complexity

### Subscription Tiers

- **Pro ($20/month):** Good for evaluation and light use
- **Max ($100/month):** Removes most rate limits; popular for daily users
- **Teams/Enterprise:** Additional features for organizations

### Managing Costs

- Use `/clear` between tasks to avoid bloated context
- Be specific to reduce iterations
- Use `/compact` strategically to preserve essential context
- For large codebases, guide Claude to specific files rather than letting it search broadly

---

## Key Habits of Successful Users

Developers getting the most from Claude Code share these habits:

1. **They plan before coding**
   - Explicit exploration and planning phases before implementation

2. **They invest in context engineering**
   - Lean, well-maintained CLAUDE.md files
   - Progressive disclosure of detailed documentation

3. **They stay specific**
   - Detailed prompts upfront rather than vague requests followed by corrections

4. **They use safety nets**
   - Tests, linting, pre-commit hooks, git branches
   - Claude is fast but imperfect

5. **They clear context aggressively**
   - Fresh sessions for fresh tasks

6. **They course-correct early**
   - Escape, rewind, redirect
   - Don't let Claude wander

---

## Getting Started

Start small:
1. Run `/init` on an existing project
2. Ask Claude some questions about the codebase
3. Try the explore-plan-code-commit workflow on a contained feature
4. Build your own patterns from there

**Remember:** Claude Code isn't magic. It's a tool that amplifies your ability to ship code—but it amplifies your mistakes too.

---

## Quick Reference Commands

| Command | Purpose |
|---------|---------|
| `claude` | Start a session |
| `/init` | Generate CLAUDE.md |
| `/clear` | Reset context |
| `/compact` | Compress context |
| `/rewind` | Jump back in history |
| `/permissions` | Manage permissions |
| `/hooks` | Configure hooks |
| `/mcp` | Check MCP servers |
| `Shift+Tab` | Cycle modes |
| `Escape` | Interrupt |
| `Escape x2` | Rewind history |

---

## References

- [Anthropic Engineering Blog: Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Claude Code Official Documentation](https://code.claude.com/docs/en/overview)
- [HumanLayer: Writing a Good CLAUDE.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md)
- [Harper Reed: Basic Claude Code](https://harper.blog/2025/05/08/basic-claude-code/)
- [Siddharth Bharath: Cooking with Claude Code](https://www.siddharthbharath.com/claude-code-the-complete-guide/)
- [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

---

## Tags

#claude-code #ai-coding #agentic-ai #developer-tools #productivity #automation #context-engineering
