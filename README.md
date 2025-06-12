# rtfmbro-mcp

> rtfmbro provides always-up-to-date, version-specific package documentation as context for coding agents. An alternative to [context7](https://github.com/upstash/context7).

https://github.com/user-attachments/assets/dbe0b3b4-a42c-4e91-8bcd-a94d430ef0b8

Demonstration of rtfmbro in use, fetching a specific version (3.1.1) of Flask's [readme](https://github.com/pallets/flask/blob/3.1.1/README.md) and [docs](https://github.com/pallets/flask/tree/3.1.1/docs).

## Overview

rtfmbro is a Model Context Protocol (MCP) server that provides real-time, version-aware documentation fetching for packages across multiple ecosystems. It bridges the gap between AI agents and accurate, up-to-date package documentation by fetching docs directly from GitHub repositories at the exact version your project uses.

## Supported Ecosystems

| Ecosystem | Registry | Status |
|-----------|----------|--------|
| **Python** | PyPI | ✅ Full Support |
| **Node.js** | npm | ✅ Full Support |
| **Swift** | SPM | 🚧 Alpha |
| **GitHub** | Direct | ⚠️ Fallback |

## Why rtfmbro?

### The Problem
- **Stale Documentation**: AI models often rely on outdated training data about packages
- **Missing Context**: Source code in `node_modules` etc. lacks high-level documentation, browsing it is usually token-consuming and inefficient
- **Version Mismatches**: Generic documentation doesn't match your specific package version, especially for legacy projects or brand new packages

### The Solution
rtfmbro tries to solve these issues by:

1. **Version-Precise Fetching**: Retrieves documentation from the exact git tag/commit that matches your lockfile
2. **Comprehensive Coverage**: Extracts all documentation files (`.md`, `.mdx`, `.txt`, `.rst`, `.html`) from the repository
3. **Intelligent Caching**: SHA-based currency checking ensures docs stay fresh without unnecessary re-fetching
4. **Agent Integration**: Seamlessly integrates with AI coding assistants via the Model Context Protocol
5. **Great DX**: Zero seting up for developers, just add the server to your MCP configuration, instructions and start fetching docs

### MCP Tools

The server exposes four primary tools to AI agents:

| Tool | Purpose | Parameters | Returns |
|------|---------|------------|---------|
| `get_readme` | Fetches and returns the README file for a specific package version | `package`, `version`, `ecosystem` | README content as string |
| `get_documentation_tree` | Generates a comprehensive folder structure of all documentation files | `package`, `version`, `ecosystem` | Tree structure as string |
| `read_files` | Reads specific documentation files with optional line range slicing | `package`, `version`, `ecosystem`, `requests[]` | Dictionary mapping paths to content |
| `search_github_repositories` | Searches for GitHub repositories using the GitHub Search API | `query`, `sort`, `order`, `per_page` | Formatted repository search results |

## Installation & Setup

### Quick Start

#### Claude Code

```bash
claude mcp add-json rtfmbro '{ "type": "http",  "url": "https://rtfmbro.smolosoft.dev/mcp/" }'
```

#### Claude Desktop / VS Code / etc.

Add the remote server to your MCP configuration:

```json
{
  "rtfmbro": {
    "type": "http", 
    "url": "https://rtfmbro.smolosoft.dev/mcp/"
  }
}
```

#### Cursor

[![Install MCP Server](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=rtfmbro&config=eyJ0eXBlIjoiaHR0cCIsInVybCI6Imh0dHBzOi8vcnRmbWJyby5zbW9sb3NvZnQuZGV2L21jcC8ifQ%3D%3D)

### Agent Integration

To truly integrate rtfmbro with your AI coding agent of choice, copy the appropriate meta-instruction file to your project:

- **GitHub Copilot**: Copy [`.github/copilot-instructions.md`](.github/copilot-instructions.md) to your project
- **Claude Code**: Copy [`CLAUDE.md`](CLAUDE.md) to your project root
- **Cursor**: Copy [`.cursor/rules`](.cursor/rules) to your project root
- **Other agents**: Adapt the instructions from either file above to your agent's format

## How It Works

### Documentation Workflow

1. **Registry Lookup**: Queries the package registry (PyPI, npm, etc.) for metadata
2. **GitHub Discovery**: Extracts the GitHub repository URL from package metadata
3. **Version Resolution**: Fetches available git tags and matches them against your semantic version
4. **Smart Fetching**: Clones the repository at the exact matched tag/commit
5. **Content Filtering**: Extracts only documentation files, removing source code and build artifacts
6. **Caching & Currency**: Stores results with SHA-based currency checking for efficient re-access

### Caching Strategy

- **SHA-Based Validation**: Compares current repository commit SHA with cached version
- **Automatic Invalidation**: Re-fetches documentation when new commits are detected
- **Persistent Storage**: Maintains local cache to avoid redundant GitHub API calls
- **Metadata Preservation**: Stores documentation tree structure for fast browsing

## Prerequisites

- Package must be published to a supported registry (PyPI, npm)
- Package metadata must contain a valid GitHub repository link
- Repository must use git tags for version management
- Documentation files must be present in the repository (not just generated sites)

## Roadmap

### Near Term
- [x] **Ecosystem independent fallback**: Implement a fallback mechanism for unsupported ecosystems
- [ ] **Provide rtfmbro source code**: Open source the server codebase
- [ ] **Public docker image**: Create a public Docker image for easy deployment
- [ ] **Private repo support**: Allow authenticated access to private repositories
- [ ] **Add Tests**: Implement unit and integration tests for core functionality
- [ ] **Enhanced Python Support**: Include pydocs and docstring extraction
- [ ] **Search Capabilities**: Search across documentation corpus

### Future Ecosystems / Languages / Registries
- [ ] **Rust** ([crates.io](https://crates.io/))
- [ ] **Go** ([pkg.go.dev](https://pkg.go.dev/))
- [ ] **Java/Kotlin** ([Maven Central](https://central.sonatype.com/))
- [ ] **C#/.NET** ([NuGet](https://www.nuget.org/))
- [ ] **Ruby** ([RubyGems](https://rubygems.org/))

### Source code hosting and repository support:
- [x] **GitHub**: Support for GitHub repositories
- [ ] **Gitlab**: Support for GitLab repositories
- [ ] **Bitbucket**: Support for Bitbucket repositories
- [ ] **Launchpad**: Support for Launchpad repositories

## Known Issues

- Some packages may have documentation in separate standalone repos
- Large repositories may take a bit of time to clone and process initially

## Similar / Additive Projects

- **[mcp-package-docs](https://github.com/sammcj/mcp-package-docs)**: Another MCP server for package documentation, focusing on documentation extraction, LSP servers, etc. May be a great supplement to rtfmbro.
- **[rust-docs-mcp-server](https://github.com/Govcraft/rust-docs-mcp-server)**: MCP server for Rust documentation, focused on Rust-specific features and documentation formats.
- **[mcp-ragdocs](https://github.com/qpd-v/mcp-ragdocs)**: MCP server for RAG (Retrieval-Augmented Generation) documentation, aimed at improving the documentation experience for AI models.
- **[godoc-mcp](https://github.com/mrjoshuak/godoc-mcp)**: MCP server for Go documentation, providing access to Go package documentation via the Model Context Protocol.
- **[context7](https://github.com/upstash/context7)**: Alternative to rtfmbro

### Differences between **context7** and **rtfmbro**
|Aspect|context7|rtfmbro|
|------|--------|-------|
| **Actuality** | Scrapes documentation ahead-of-time at intransparent intervals or upon user trigger. As of writing, the "latest" Next.js docs are already 2 days old.| Fetches documentation just-in-time, ensuring it's always up-to-date. |
| **Version-specific docs** | Theoretically allows scraping older versions (useful for legacy or longtime projects), but the process is complicated, limiting practical availability effectively to latest versions. | Fetches older documentation just-in-time, and always remains current, identical to latest docs. |
| **Search strategy** | Uses either A) optionally token-limited RAG search to filter/preprocess docs which can be hit-or-miss, or B) dumps all content into LLM's context, resulting in excessive token-use. | Employs agentic discovery ([as used by Claude Code itself](https://x.com/pashmerepat/status/1926717705660375463)) |
| **Developer Experience** | Requires explicitly mention of context7 in every prompt. | Operates via defined rules/instructions, auto-selecting appropriate package name/version from lock file ("set and forget"). |
| **Support** | Language/ecosystem independent. | Currently language/ecosystem-specific; planned additional languages/ecosystems and language-independent fallback mechanism soon. |
