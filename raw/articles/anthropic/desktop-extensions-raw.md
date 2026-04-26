---
title: Desktop Extensions: One-click MCP server installation for Claude Desktop
url: https://www.anthropic.com/engineering/desktop-extensions
source: anthropic/engineering
saved: 2026-04-26
type: raw-article
tags: [anthropic, engineering, llm, agents]
---

# Desktop Extensions: One-click MCP server installation for Claude Desktop
Published Jun 26, 2025
Desktop Extensions make installing MCP servers as easy as clicking a button. We share the technical architecture and tips for creating good extensions.
- File extension update
Sep 11, 2025
Claude Desktop Extensions now use the .mcpb (MCP Bundle) file extension instead of .dxt. Existing .dxt extensions will continue to work, but we recommend developers use .mcpb for new extensions going forward. All functionality remains the same - this is purely a naming convention update.
—
When we released the Model Context Protocol (MCP) last year, we saw developers build amazing local servers that gave Claude access to everything from file systems to databases. But we kept hearing the same feedback: installation was too complex. Users needed developer tools, had to manually edit configuration files, and often got stuck on dependency issues.
Today, we&#x27;re introducing Desktop Extensions—a new packaging format that makes installing MCP servers as simple as clicking a button.
### Addressing the MCP installation problem
Local MCP servers unlock powerful capabilities for Claude Desktop users. They can interact with local applications, access private data, and integrate with development tools—all while keeping data on the user&#x27;s machine. However, the current installation process creates significant barriers:
- Developer tools required: Users need Node.js, Python, or other runtimes installed
- Manual configuration: Each server requires editing JSON configuration files
- Dependency management: Users must resolve package conflicts and version mismatches
- No discovery mechanism: Finding useful MCP servers requires searching GitHub
- Update complexity: Keeping servers current means manual reinstallation
These friction points meant that MCP servers, despite their power, remained largely inaccessible to non-technical users.
### Introducing Desktop Extensions
Desktop Extensions (.mcpb files) solve these problems by bundling an entire MCP server—including all dependencies—into a single installable package. Here&#x27;s what changes for users:
Before:
# Install Node.js first
npm install -g @example/mcp-server
# Edit ~/.claude/claude_desktop_config.json manually
# Restart Claude Desktop
# Hope it worksCopyAfter:
- Download a .mcpb file
- Double-click to open with Claude Desktop
- Click &quot;Install&quot;
That&#x27;s it. No terminal, no configuration files, no dependency conflicts.
## Architecture overview
A Desktop Extension is a zip archive containing the local MCP server as well as a manifest.json, which describes everything Claude Desktop and other apps supporting desktop extensions need to know.
extension.mcpb (ZIP archive)
├── manifest.json # Extension metadata and configuration
├── server/ # MCP server implementation
│ └── [server files]
├── dependencies/ # All required packages/libraries
└── icon.png # Optional: Extension icon
# Example: Node.js Extension
extension.mcpb
├── manifest.json # Required: Extension metadata and configuration
├── server/ # Server files
│ └── index.js # Main entry point
├── node_modules/ # Bundled dependencies
├── package.json # Optional: NPM package definition
└── icon.png # Optional: Extension icon
# Example: Python Extension
extension.mcpb (ZIP file)
├── manifest.json # Required: Extension metadata and configuration
├── server/ # Server files
│ ├── main.py # Main entry point
│ └── utils.py # Additional modules
├── lib/ # Bundled Python packages
├── requirements.txt # Optional: Python dependencies list
└── icon.png # Optional: Extension iconCopyThe only required file in a Desktop Extension is a manifest.json. Claude Desktop handles all the complexity:
- Built-in runtime: We ship Node.js with Claude Desktop, eliminating external dependencies
- Automatic updates: Extensions update automatically when new versions are available
- Secure secrets: Sensitive configuration like API keys are stored in the OS keychain
The manifest contains human-readable information (like the name, description, or author), a declaration of features (tools, prompts), user configuration, and runtime requirements. Most fields are optional, so the minimal version is quite short, although in practice, we expect all three supported extension types (Node.js, Python, and classic binaries/executables) to include files:
{
&quot;mcpb_version&quot;: &quot;0.1&quot;, // MCPB spec version this manifest conforms to
&quot;name&quot;: &quot;my-extension&quot;, // Machine-readable name (used for CLI, APIs)
&quot;version&quot;: &quot;1.0.0&quot;, // Semantic version of your extension
&quot;description&quot;: &quot;A simple MCP extension&quot;, // Brief description of what the extension does
&quot;author&quot;: { // Author information (required)
&quot;name&quot;: &quot;Extension Author&quot; // Author&#x27;s name (required field)
},
&quot;server&quot;: { // Server configuration (required)
&quot;type&quot;: &quot;node&quot;, // Server type: &quot;node&quot;, &quot;python&quot;, or &quot;binary&quot;
&quot;entry_point&quot;: &quot;server/index.js&quot;, // Path to the main server file
&quot;mcp_config&quot;: { // MCP server configuration
&quot;command&quot;: &quot;node&quot;, // Command to run the server
&quot;args&quot;: [ // Arguments passed to the command
&quot;${__dirname}/server/index.js&quot; // ${__dirname} is replaced with the extension&#x27;s directory
]
}
}
}
CopyThere are a number of convenience options available in the manifest spec that aim to make the installation and configuration of local MCP servers easier. The server configuration object can be defined in a way that makes room both for user-defined configuration in the form of template literals as well as platform-specific overrides. Extension developers can define, in detail, what kind of configuration they want to collect from users.
Let’s take a look at a concrete example of how the manifest aids with configuration. In the manifest below, the developer declares that the user needs to supply an api_key. Claude will not enable the extension until the user has supplied that value, keep it automatically in the operating system’s secret vault, and transparently replace the ${user_config.api_key} with the user-supplied value when launching the server. Similarly, ${__dirname} will be replaced with the full path to the extension’s unpacked directory.
{
&quot;mcpb_version&quot;: &quot;0.1&quot;,
&quot;name&quot;: &quot;my-extension&quot;,
&quot;version&quot;: &quot;1.0.0&quot;,
&quot;description&quot;: &quot;A simple MCP extension&quot;,
&quot;author&quot;: {
&quot;name&quot;: &quot;Extension Author&quot;
},
&quot;server&quot;: {
&quot;type&quot;: &quot;node&quot;,
&quot;entry_point&quot;: &quot;server/index.js&quot;,
&quot;mcp_config&quot;: {
&quot;command&quot;: &quot;node&quot;,
&quot;args&quot;: [&quot;${__dirname}/server/index.js&quot;],
&quot;env&quot;: {
&quot;API_KEY&quot;: &quot;${user_config.api_key}&quot;
}
}
},
&quot;user_config&quot;: {
&quot;api_key&quot;: {