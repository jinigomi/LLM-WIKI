---
title: Introducing advanced tool use on the Claude Developer Platform
url: https://www.anthropic.com/engineering/advanced-tool-use
source: anthropic/engineering
saved: 2026-04-26
type: raw-article
tags: [anthropic, engineering, llm, agents]
---

# Introducing advanced tool use on the Claude Developer Platform
Published Nov 24, 2025
We’ve added three new beta features that let Claude discover, learn, and execute tools dynamically. Here’s how they work.
The future of AI agents is one where models work seamlessly across hundreds or thousands of tools. An IDE assistant that integrates git operations, file manipulation, package managers, testing frameworks, and deployment pipelines. An operations coordinator that connects Slack, GitHub, Google Drive, Jira, company databases, and dozens of MCP servers simultaneously.
To build effective agents, they need to work with unlimited tool libraries without stuffing every definition into context upfront. Our blog article on using code execution with MCP discussed how tool results and definitions can sometimes consume 50,000+ tokens before an agent reads a request. Agents should discover and load tools on-demand, keeping only what&#x27;s relevant for the current task.
Agents also need the ability to call tools from code. When using natural language tool calling, each invocation requires a full inference pass, and intermediate results pile up in context whether they&#x27;re useful or not. Code is a natural fit for orchestration logic, such as loops, conditionals, and data transformations. Agents need the flexibility to choose between code execution and inference based on the task at hand.
Agents also need to learn correct tool usage from examples, not just schema definitions. JSON schemas define what&#x27;s structurally valid, but can&#x27;t express usage patterns: when to include optional parameters, which combinations make sense, or what conventions your API expects.
Today, we&#x27;re releasing three features that make this possible:
- Tool Search Tool, which allows Claude to use search tools to access thousands of tools without consuming its context window
- Programmatic Tool Calling, which allows Claude to invoke tools in a code execution environment reducing the impact on the model’s context window
- Tool Use Examples, which provides a universal standard for demonstrating how to effectively use a given tool
In internal testing, we’ve found these features have helped us build things that wouldn’t have been possible with conventional tool use patterns. For example, Claude for Excel uses Programmatic Tool Calling to read and modify spreadsheets with thousands of rows without overloading the model’s context window.
Based on our experience, we believe these features open up new possibilities for what you can build with Claude.
## Tool Search Tool
### The challenge
MCP tool definitions provide important context, but as more servers connect, those tokens can add up. Consider a five-server setup:
- GitHub: 35 tools (~26K tokens)
- Slack: 11 tools (~21K tokens)
- Sentry: 5 tools (~3K tokens)
- Grafana: 5 tools (~3K tokens)
- Splunk: 2 tools (~2K tokens)
That&#x27;s 58 tools consuming approximately 55K tokens before the conversation even starts. Add more servers like Jira (which alone uses ~17K tokens) and you&#x27;re quickly approaching 100K+ token overhead. At Anthropic, we&#x27;ve seen tool definitions consume 134K tokens before optimization.
But token cost isn&#x27;t the only issue. The most common failures are wrong tool selection and incorrect parameters, especially when tools have similar names like notification-send-user vs. notification-send-channel.
### Our solution
Instead of loading all tool definitions upfront, the Tool Search Tool discovers tools on-demand. Claude only sees the tools it actually needs for the current task.
Tool Search Tool preserves 191,300 tokens of context compared to 122,800 with Claude’s traditional approach.
Traditional approach:
- All tool definitions loaded upfront (~72K tokens for 50+ MCP tools)
- Conversation history and system prompt compete for remaining space
- Total context consumption: ~77K tokens before any work begins
With the Tool Search Tool:
- Only the Tool Search Tool loaded upfront (~500 tokens)
- Tools discovered on-demand as needed (3-5 relevant tools, ~3K tokens)
- Total context consumption: ~8.7K tokens, preserving 95% of context window
This represents an 85% reduction in token usage while maintaining access to your full tool library. Internal testing showed significant accuracy improvements on MCP evaluations when working with large tool libraries. Opus 4 improved from 49% to 74%, and Opus 4.5 improved from 79.5% to 88.1% with Tool Search Tool enabled.
### How the Tool Search Tool works
The Tool Search Tool lets Claude dynamically discover tools instead of loading all definitions upfront. You provide all your tool definitions to the API, but mark tools with defer_loading: true to make them discoverable on-demand. Deferred tools aren&#x27;t loaded into Claude&#x27;s context initially. Claude only sees the Tool Search Tool itself plus any tools with defer_loading: false (your most critical, frequently-used tools).
When Claude needs specific capabilities, it searches for relevant tools. The Tool Search Tool returns references to matching tools, which get expanded into full definitions in Claude&#x27;s context.
For example, if Claude needs to interact with GitHub, it searches for &quot;github,&quot; and only github.createPullRequest and github.listIssues get loaded—not your other 50+ tools from Slack, Jira, and Google Drive.
This way, Claude has access to your full tool library while only paying the token cost for tools it actually needs.
Prompt caching note: Tool Search Tool doesn&#x27;t break prompt caching because deferred tools are excluded from the initial prompt entirely. They&#x27;re only added to context after Claude searches for them, so your system prompt and core tool definitions remain cacheable.
Implementation:
{
&quot;tools&quot;: [
// Include a tool search tool (regex, BM25, or custom)
{&quot;type&quot;: &quot;tool_search_tool_regex_20251119&quot;, &quot;name&quot;: &quot;tool_search_tool_regex&quot;},
// Mark tools for on-demand discovery
{
&quot;name&quot;: &quot;github.createPullRequest&quot;,
&quot;description&quot;: &quot;Create a pull request&quot;,
&quot;input_schema&quot;: {...},
&quot;defer_loading&quot;: true
}
// ... hundreds more deferred tools with defer_loading: true
]
}
CopyFor MCP servers, you can defer loading entire servers while keeping specific high-use tools loaded:
{
&quot;type&quot;: &quot;mcp_toolset&quot;,
&quot;mcp_server_name&quot;: &quot;google-drive&quot;,
&quot;default_config&quot;: {&quot;defer_loading&quot;: true}, # defer loading the entire server
&quot;configs&quot;: {
&quot;search_files&quot;: {
&quot;defer_loading&quot;: false
} // Keep most used tool loaded
}
}CopyThe Claude Developer Platform provides regex-based and BM25-based search tools out of the box, but you can also implement custom search tools using embeddings or other strategies.
### When to use the Tool Search Tool
Like any architectural decision, enabling the Tool Search Tool involves trade-offs. The feature adds a search step before tool invocation, so it delivers the best ROI when the context savings and accuracy improvements outweigh additional latency.
Use it when:
- Tool definitions consuming &gt;10K tokens
- Experiencing tool selection accuracy issues
- Building MCP-powered systems with multiple servers
- 10+ tools available
Less beneficial when:
- Small tool library (&lt;10 tools)
- All tools used frequently in every session
- Tool definitions are compact
## Programmatic Tool Calling
### The challenge
Traditional tool calling creates two fundamental problems as workflows become more complex:
- Context pollution from intermediate results: When Claude analyzes a 10MB log file for error patterns, the entire file enters its context window, even though Claude only needs a summary of error frequencies. When fetching customer data across multiple tables, every record accumulates in context regardless of relevance. These intermediate results consume massive token budgets and can push important information out of the context window entirely.
- Inference overhead and manual synthesis: Each tool call requires a full model inference pass. After receiving results, Claude must &quot;eyeball&quot; the data to extract relevant information, reason about how pieces fit together, and decide what to do next—all through natural language processing. A five tool workflow means five inference passes plus Claude parsing each result, comparing values, and synthesizing conclusions. This is both slow and error-prone.
### Our solution
Programmatic Tool Calling enables Claude to orchestrate tools through code rather than through individual API round-trips. Instead of Claude requesting tools one at a time with each result being returned to its context, Claude writes code that calls multiple tools, processes their outputs, and controls what information actually enters its context window.
Claude excels at writing code and by letting it express orchestration logic in Python rather than through natural language tool invocations, you get more reliable, precise control flow. Loops, conditionals, data transformations, and error handling are all explicit in code rather than implicit in Claude&#x27;s reasoning.
Example: Budget compliance checkConsider a common business task: &quot;Which team members exceeded their Q3 travel budget?&quot;
You have three tools available:
- get_team_members(department) - Returns team member list with IDs and levels
- get_expenses(user_id, quarter) - Returns expense line items for a user
- get_budget_by_level(level) - Returns budget limits for an employee level
Traditional approach:
- Fetch team members → 20 people
- For each person, fetch their Q3 expenses → 20 tool calls, each returning 50-100 line items (flights, hotels, meals, receipts)
- Fetch budget limits by employee level
- All of this enters Claude&#x27;s context: 2,000+ expense line items (50 KB+)
- Claude manually sums each person&#x27;s expenses, looks up their budget, compares expenses against budget limits
- More round-trips to the model, significant context consumption
With Programmatic Tool Calling:
Instead of each tool result returning to Claude, Claude writes a Python script that orchestrates the entire workflow. The script runs in the Code Execution tool (a sandboxed environment), pausing when it needs results from your tools. When you return tool results via the API, they&#x27;re processed by the script rather than consumed by the model. The script continues executing, and Claude only sees the final output.