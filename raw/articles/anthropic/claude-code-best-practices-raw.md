---
title: Claude Code: Best practices for agentic coding
url: https://www.anthropic.com/engineering/claude-code-best-practices
source: anthropic/engineering
saved: 2026-04-26
type: raw-article
tags: [anthropic, engineering, llm, agents]
---

- Best Practices for Claude Code - Claude Code DocsSkip to main contentClaude Code Docs home pageEnglishSearch...⌘KAsk AIClaude Developer Platform
- Claude Code on the Web
- Claude Code on the Web
Search...NavigationUse Claude CodeBest Practices for Claude CodeGetting startedBuild with Claude CodeDeploymentAdministrationConfigurationReferenceAgent SDKWhat&#x27;s NewResourcesGetting started- Overview
- Quickstart
- Changelog
Core concepts- How Claude Code works
- Extend Claude Code
- Explore the .claude directory
- Explore the context window
Use Claude Code- Store instructions and memories
- Permission modes
- Common workflows
- Best practices
Platforms and integrations- Overview
- Remote Control
- Claude Code on the web
- Claude Code on desktop
- Chrome extension (beta)
- Computer use (preview)
- Visual Studio Code
- JetBrains IDEs
- Code review &amp; CI/CD
- Claude Code in Slack
On this page- Give Claude a way to verify its work
- Explore first, then plan, then code
- Provide specific context in your prompts
- Provide rich content
- Configure your environment
- Write an effective CLAUDE.md
- Configure permissions
- Use CLI tools
- Connect MCP servers
- Set up hooks
- Create skills
- Create custom subagents
- Install plugins
- Communicate effectively
- Ask codebase questions
- Let Claude interview you
- Manage your session
- Course-correct early and often
- Manage context aggressively
- Use subagents for investigation
- Rewind with checkpoints
- Resume conversations
- Automate and scale
- Run non-interactive mode
- Run multiple Claude sessions
- Fan out across files
- Run autonomously with auto mode
- Avoid common failure patterns
- Develop your intuition
- Related resources
Use Claude Code
# Best Practices for Claude Code
Copy pageTips and patterns for getting the most out of Claude Code, from configuring your environment to scaling across parallel sessions.
Copy pageClaude Code is an agentic coding environment. Unlike a chatbot that answers questions and waits, Claude Code can read your files, run commands, make changes, and autonomously work through problems while you watch, redirect, or step away entirely.
This changes how you work. Instead of writing code yourself and asking Claude to review it, you describe what you want and Claude figures out how to build it. Claude explores, plans, and implements.
But this autonomy still comes with a learning curve. Claude works within certain constraints you need to understand.
This guide covers patterns that have proven effective across Anthropic’s internal teams and for engineers using Claude Code across various codebases, languages, and environments. For how the agentic loop works under the hood, see How Claude Code works.
Most best practices are based on one constraint: Claude’s context window fills up fast, and performance degrades as it fills.
Claude’s context window holds your entire conversation, including every message, every file Claude reads, and every command output. However, this can fill up fast. A single debugging session or codebase exploration might generate and consume tens of thousands of tokens.
This matters since LLM performance degrades as context fills. When the context window is getting full, Claude may start “forgetting” earlier instructions or making more mistakes. The context window is the most important resource to manage. To see how a session fills up in practice, watch an interactive walkthrough of what loads at startup and what each file read costs. Track context usage continuously with a custom status line, and see Reduce token usage for strategies on reducing token usage.
## ​Give Claude a way to verify its work
Include tests, screenshots, or expected outputs so Claude can check itself. This is the single highest-leverage thing you can do.
Claude performs dramatically better when it can verify its own work, like run tests, compare screenshots, and validate outputs.
Without clear success criteria, it might produce something that looks right but actually doesn’t work. You become the only feedback loop, and every mistake requires your attention.
StrategyBeforeAfterProvide verification criteria”implement a function that validates email addresses&quot;&quot;write a validateEmail function. example test cases: user@example.com is true, invalid is false, user@.com is false. run the tests after implementing”Verify UI changes visually”make the dashboard look better&quot;&quot;[paste screenshot] implement this design. take a screenshot of the result and compare it to the original. list differences and fix them”Address root causes, not symptoms”the build is failing&quot;&quot;the build fails with this error: [paste error]. fix it and verify the build succeeds. address the root cause, don’t suppress the error”
UI changes can be verified using the Claude in Chrome extension. It opens new tabs in your browser, tests the UI, and iterates until the code works.
Your verification can also be a test suite, a linter, or a Bash command that checks output. Invest in making your verification rock-solid.
## ​Explore first, then plan, then code
Separate research and planning from implementation to avoid solving the wrong problem.
Letting Claude jump straight to coding can produce code that solves the wrong problem. Use Plan Mode to separate exploration from execution.
The recommended workflow has four phases:
1Explore
Enter Plan Mode. Claude reads files and answers questions without making changes.claude (Plan Mode)read /src/auth and understand how we handle sessions and login.
also look at how we manage environment variables for secrets.
2Plan
Ask Claude to create a detailed implementation plan.claude (Plan Mode)I want to add Google OAuth. What files need to change?
What&#x27;s the session flow? Create a plan.
Press Ctrl+G to open the plan in your text editor for direct editing before Claude proceeds.3Implement
Switch back to Normal Mode and let Claude code, verifying against its plan.claude (Normal Mode)implement the OAuth flow from your plan. write tests for the
callback handler, run the test suite and fix any failures.
4Commit
Ask Claude to commit with a descriptive message and create a PR.claude (Normal Mode)commit with a descriptive message and open a PR
Plan Mode is useful, but also adds overhead.For tasks where the scope is clear and the fix is small (like fixing a typo, adding a log line, or renaming a variable) ask Claude to do it directly.Planning is most useful when you’re uncertain about the approach, when the change modifies multiple files, or when you’re unfamiliar with the code being modified. If you could describe the diff in one sentence, skip the plan.
## ​Provide specific context in your prompts
The more precise your instructions, the fewer corrections you’ll need.
Claude can infer intent, but it can’t read your mind. Reference specific files, mention constraints, and point to example patterns.
StrategyBeforeAfterScope the task. Specify which file, what scenario, and testing preferences.”add tests for foo.py&quot;&quot;write a test for foo.py covering the edge case where the user is logged out. avoid mocks.”Point to sources. Direct Claude to the source that can answer a question.”why does ExecutionFactory have such a weird api?&quot;&quot;look through ExecutionFactory’s git history and summarize how its api came to be”Reference existing patterns. Point Claude to patterns in your codebase.”add a calendar widget&quot;&quot;look at how existing widgets are implemented on the home page to understand the patterns. HotDogWidget.php is a good example. follow the pattern to implement a new calendar widget that lets the user select a month and paginate forwards/backwards to pick a year. build from scratch without libraries other than the ones already used in the codebase.”Describe the symptom. Provide the symptom, the likely location, and what “fixed” looks like.”fix the login bug&quot;&quot;users report that login fails after session timeout. check the auth flow in src/auth/, especially token refresh. write a failing test that reproduces the issue, then fix it”
Vague prompts can be useful when you’re exploring and can afford to course-correct. A prompt like &quot;what would you improve in this file?&quot; can surface things you wouldn’t have thought to ask about.
### ​Provide rich content
Use @ to reference files, paste screenshots/images, or pipe data directly.
You can provide rich data to Claude in several ways:
- Reference files with @ instead of describing where code lives. Claude reads the file before responding.
- Paste images directly. Copy/paste or drag and drop images into the prompt.
- Give URLs for documentation and API references. Use /permissions to allowlist frequently-used domains.
- Pipe in data by running cat error.log | claude to send file contents directly.
- Let Claude fetch what it needs. Tell Claude to pull context itself using Bash commands, MCP tools, or by reading files.