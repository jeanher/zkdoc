# ZK Documentation MCP Server

An [MCP (Model Context Protocol) server](https://modelcontextprotocol.io/docs/learn/server-concepts) for the [ZK Framework documentation](https://github.com/zkoss/zkdoc) that provides semantic search, intelligent Q&A, and documentation indexing capabilities. It feeds official ZK knowledge to the AI tools you choose, improving the accuracy of ZK-related answers and helping you develop ZK applications more efficiently.

## Overview

This server integrates [Chroma](https://docs.trychroma.com/docs/overview/introduction) for vector-based semantic search and implements MCP tools for searching [ZK documentation](https://github.com/zkoss/zkdoc).

You can use this MCP server with any AI tool that supports MCP servers to:
- Retrieving specific documentation content
- Answering questions based on ZK documentation
- Managing documentation indices
- Submit your feedback aboutZK documentation

## Quick Start

### Prerequisites
Your environment should have the following installed:
- Python 3.10 or higher.
- `uv` package manager (https://docs.astral.sh/uv/) - optional but recommended
- **Git** (2.7 or higher) - Required for cloning ZK documentation from the Github repository


### Installation zk doc MCP server from PyPI

create a virtual environment of Python 3.10
```bash
uv venv --python 3.10
```

```bash
# Using uv (recommended)
uv pip install zk-doc-mcp-server
# This command installs the package into the currently active virtual environment.
```


List installed package to check
```bash
uv pip list
```

Upgrade it if you installed an older version
```bash
uv pip install --upgrade zk-doc-mcp-server
```

The package is available at [PyPI](https://pypi.org/project/zk-doc-mcp-server/)

### Initialize Documentation (Optional Setup)

After installation, you can optionally pre-index the ZK documentation for faster server startup:

```bash
uvx zk-doc-mcp-server init
```

This command:
1. **Clones/syncs** the documentation repository to `ZK_DOC_SRC_DIR`
2. **Pre-indexes** the documentation for semantic search

After running `init`, subsequent server startups can be immediately ready for doc search. Otherwise, you might need to wait for several minutes for background indexing.

**Check status or re-index after documentation updates:**
```bash
# Re-sync and re-index if documentation has changed
uvx zk-doc-mcp-server init

# Force complete re-clone and re-index
uvx zk-doc-mcp-server init --force
```
The server uses **incremental indexing** that automatically detects documentation changes and only re-indexes modified files, making updates fast and efficient.

**When to use init:**
- First-time setup
- After significant documentation updates
- When you want immediate search availability without waiting for background indexing

### Setup for AI Tool
Most AI tools support using an MCP server, please check their document to know how to configure it. Here we just give some examples.

**Using with Claude Code**
```bash
claude mcp add zk-doc -- uvx zk-doc-mcp-server
```

**Using with Gemini CLI**

Add the MCP server to your Gemini configuration file (typically `~/.gemini/config.json` or similar):
```json
{
  "mcpServers": {
    "zk-doc": {
      "command": "uvx",
      "args": ["zk-doc-mcp-server"]
    }
  }
}
```

### Start using it
Ask your AI tool to search zk doc like:
```
Search the ZK doc for "what is desktop"
```

## MCP Tools

### search_zk_docs
Search ZK documentation for relevant content using semantic search.

**Parameters:**
- `query` (string, required): Search query
- `limit` (integer, optional, default: 5): Maximum results to return (1-20)

**Response:**
```json
{
  "results": [],
  "query": "your search query",
  "limit": 5,
  "message": "Search functionality coming soon"
}
```

### submit_feedback
Submit feedback about search results to improve documentation.

When search results don't meet user expectations, this tool captures feedback that helps the documentation team understand gaps and improve content.

**Parameters:**
- `query` (string, required): The search query that produced unsatisfactory results
- `results` (list, required): List of search results returned (each with title, file_path, content)
- `expected` (string, required): What the user expected to find
- `comments` (string, optional): Additional context about why results don't match

**Features:**
- Feedback is **always saved locally** to `~/.zk-doc-mcp/feedback/`
- Automatically **submitted as GitHub issue** to https://github.com/zkoss/zkdoc/issues
- **Non-blocking operation** - returns immediately while GitHub submission happens in background
- **Graceful fallback** - feedback is preserved locally if network fails

**Response:**
```json
{
  "success": true,
  "feedback_id": "feedback_20250114_a7k9m2x8",
  "local_path": "/home/user/.zk-doc-mcp/feedback/feedback_20250114_a7k9m2x8.json",
  "github_issue_url": "https://github.com/zkoss/zkdoc/issues/456",
  "message": "Feedback saved and submitted to https://github.com/zkoss/zkdoc/issues/456"
}
```

### show_settings
Display all configuration settings for the ZK Doc MCP Server.

This tool enables you to inspect the current server configuration, including all environment variables and their effective values. It's useful for debugging, configuration verification, and discovering available settings.

**Parameters:**
None - this tool takes no parameters.

**Response:**
```json
{
  "settings": {
    "ZK_DOC_SRC_DIR": "~/.zk-doc-mcp/repo",
    "ZK_DOC_VECTOR_DB_DIR": "~/.zk-doc-mcp/chroma_db",
    "ZK_DOC_FORCE_REINDEX": false,
    "ZK_DOC_LOG_LEVEL": "INFO",
    "ZK_DOC_USE_GIT": true,
    "ZK_DOC_CLONE_METHOD": "https",
    "ZK_DOC_REPO_URL": "https://github.com/zkoss/zkdoc.git",
    "ZK_DOC_GIT_BRANCH": "master",
    "ZK_DOC_FEEDBACK_ENABLED": true,
    "ZK_DOC_FEEDBACK_RETENTION_DAYS": 90,
    "ZK_DOC_FEEDBACK_GITHUB_REPO": "zkoss/zkdoc"
  },
  "summary": {
    "total_settings": 11,
    "git_enabled": true,
    "feedback_enabled": true
  }
}
```

**Usage in Claude Code:**
```
Show me the current ZK doc MCP server settings
```

**Usage in Gemini CLI:**
```
Use the show_settings tool from zk-doc server
```

**Common use cases:**
- Verify that Git synchronization is enabled/disabled
- Check which documentation branch is being used
- Confirm the vector database location
- Debug configuration issues
- Discover available configuration options


## Configuration

The MCP server behavior can be customized using environment variables. These settings control documentation sources, indexing, and Git integration.

### Available Settings

The server provides the following configurable settings:

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `ZK_DOC_SRC_DIR` | string | `~/.zk-doc-mcp/repo` | Documentation source directory (Git repo or local docs) |
| `ZK_DOC_VECTOR_DB_DIR` | string | `~/.zk-doc-mcp/chroma_db` | Vector database directory for storing embeddings and search indices |
| `ZK_DOC_FORCE_REINDEX` | boolean | `false` | Force re-indexing of documentation on startup |
| `ZK_DOC_LOG_LEVEL` | enum | `INFO` | Logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL) |
| `ZK_DOC_USE_GIT` | boolean | `true` | Enable Git synchronization for documentation |
| `ZK_DOC_CLONE_METHOD` | enum | `https` | Git clone method (`https` or `ssh`) |
| `ZK_DOC_REPO_URL` | string | `https://github.com/zkoss/zkdoc.git` | Repository URL to clone documentation from |
| `ZK_DOC_GIT_BRANCH` | string | `master` | Git branch to pull documentation from |
| `ZK_DOC_FEEDBACK_ENABLED` | boolean | `true` | Enable feedback collection for search improvements |
| `ZK_DOC_FEEDBACK_RETENTION_DAYS` | integer | `90` | Days to retain local feedback files |
| `ZK_DOC_FEEDBACK_GITHUB_REPO` | string | `zkoss/zkdoc` | GitHub repository for feedback issues (built-in) |


### Example Usages

#### Example: Using SSH for Git Clone

To clone the documentation repository using SSH instead of HTTPS:

```bash
export ZK_DOC_CLONE_METHOD=ssh
export ZK_DOC_USE_GIT=true
uv run python3 -m zk_doc_mcp
```

**Prerequisites for SSH:**
- SSH key configured and added to ssh-agent
- SSH key authorized on GitHub (or your Git hosting service)

#### Example: Using a Custom Documentation Directory

To use a local documentation directory instead of cloning from Git:

```bash
# Disable Git sync and point to local directory
export ZK_DOC_USE_GIT=false
export ZK_DOC_SRC_DIR=/path/to/local/docs
uv run python3 -m zk_doc_mcp
```

#### Example: Force Re-indexing Documentation

To rebuild the vector search index from scratch:

```bash
export ZK_DOC_FORCE_REINDEX=true
uv run python3 -m zk_doc_mcp
```

After re-indexing completes, the server will run normally with the updated index.

### Configuration Verification

Use the `show_settings` tool to verify your configuration is what you expect.

## License

[MIT License](LICENSE)
