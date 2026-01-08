# Cline MCP Integration Collection

A guide to setting up the GitHub MCP server with Cline using Docker.

English | [简体中文](README.zh-CN.md)

## What is MCP?

The Model Context Protocol (MCP) is an open standard that enables AI assistants like Cline to securely connect to external data sources and tools. MCP servers provide additional capabilities through tools and resources that extend Cline's functionality.

## Table of Contents

- [Prerequisites](#prerequisites)
- [GitHub MCP Server](#github-mcp-server)
  - [Setup Steps](#setup-steps)
  - [Configuration](#configuration)
  - [Features](#features)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

## Prerequisites

Before setting up the GitHub MCP server, ensure you have:

- **Cline** installed in Visual Studio Code
- **Docker** installed and running on your system
- **GitHub Personal Access Token** with appropriate permissions

## GitHub MCP Server

The GitHub MCP server enables interaction with GitHub repositories, issues, pull requests, and more through Cline.

### Setup Steps

1. **Create a GitHub Personal Access Token**

   - Go to https://github.com/settings/tokens
   - Click "Generate new token" (classic)
   - Grant appropriate permissions:
     - `repo` - Full control of private repositories
     - `read:org` - Read org and team membership
     - `read:user` - Read user profile data
     - `user:email` - Access user email addresses
   - Copy the generated token

2. **Configure Cline MCP Settings**

   - Open VS Code Settings (Ctrl/Cmd + ,)
   - Search for "Cline MCP"
   - Click "Edit in settings.json"
   - Add the GitHub MCP server configuration (see below)

3. **Update Environment Variables**
   - Replace `xxx` in the configuration with your GitHub Personal Access Token
   - Set `GITHUB_READ_ONLY` to `true` if you want read-only access, or `false` for full access

### Configuration

Add this configuration to your Cline MCP settings:

```json
{
  "cline.mcpServers": {
    "github-mcp": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "-e",
        "GITHUB_READ_ONLY",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "xxx",
        "GITHUB_READ_ONLY": "false"
      },
      "disabled": false
    }
  }
}
```

**Configuration Parameters:**

- `command`: Uses Docker to run the GitHub MCP server container
- `args`: Docker run arguments including environment variable passthrough
- `env.GITHUB_PERSONAL_ACCESS_TOKEN`: Your GitHub Personal Access Token
- `env.GITHUB_READ_ONLY`: Set to `"true"` for read-only mode, `"false"` for full access
- `disabled`: Set to `false` to enable the server, `true` to disable

**Using System Environment Variables:**

You can also reference system environment variables:

```json
{
  "cline.mcpServers": {
    "github-mcp": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "-e",
        "GITHUB_READ_ONLY",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}",
        "GITHUB_READ_ONLY": "false"
      },
      "disabled": false
    }
  }
}
```

### Features

The GitHub MCP server provides the following capabilities:

**Repository Management:**

- Search repositories
- Get repository contents
- Create and manage branches
- Fork repositories

**Issue Management:**

- List and search issues
- Create new issues
- Update issue details
- Add comments to issues
- Manage issue labels and assignees

**Pull Request Operations:**

- List and search pull requests
- Create new pull requests
- Review pull requests
- Add review comments
- Merge pull requests
- Update pull request branches

**Commit Operations:**

- View commit history
- Get commit details
- Create and update files via commits

**Code Operations:**

- Search code across repositories
- Read file contents
- Create or update files
- Delete files

**Organization & Team:**

- List teams
- Get team members

**Additional Features:**

- Request Copilot code reviews
- Assign Copilot to issues
- Manage releases and tags
- Work with GitHub Actions

## Troubleshooting

### Common Issues

**Docker Not Running:**

- Ensure Docker Desktop is installed and running
- Verify Docker is accessible: `docker --version`
- Check Docker daemon is active

**Authentication Errors:**

- Verify your GitHub token is valid and not expired
- Ensure the token has the required scopes/permissions
- Check that the token is correctly set in the configuration
- Try regenerating your GitHub Personal Access Token

**Container Pull Issues:**

- Ensure you have internet connectivity
- Check if Docker can pull images: `docker pull ghcr.io/github/github-mcp-server`
- Verify no corporate firewall is blocking GitHub Container Registry

**Server Not Starting:**

- Review VS Code Developer Tools (Help > Toggle Developer Tools) for error messages
- Check the Cline output panel for MCP server logs
- Verify the configuration JSON is valid (no syntax errors)

**Permission Denied:**

- Ensure your GitHub token has the necessary permissions for the operations you're attempting
- If using read-only mode, verify operations are read-only
- Check repository access permissions

### Debugging

Enable MCP logging in Cline settings:

```json
{
  "cline.mcpDebug": true
}
```

Check logs in VS Code Output panel (View > Output > Cline)

### Testing the Setup

After configuration, restart VS Code and verify the GitHub MCP server is connected:

1. Open Cline
2. The GitHub MCP tools should be available
3. Try a simple command like listing your repositories

### Getting Help

- [Cline Documentation](https://docs.cline.bot)
- [MCP Documentation](https://modelcontextprotocol.io)
- [GitHub MCP Server Repository](https://github.com/github/github-mcp-server)
- [Cline GitHub Issues](https://github.com/cline/cline/issues)

## Additional Resources

- [GitHub Personal Access Tokens Documentation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [Docker Documentation](https://docs.docker.com/)
- [Official MCP Specification](https://spec.modelcontextprotocol.io)
- [GitHub REST API Documentation](https://docs.github.com/en/rest)

## Security Best Practices

- **Never commit your GitHub token to version control**
- Use environment variables or VS Code settings for sensitive data
- Regularly rotate your GitHub Personal Access Tokens
- Use read-only mode when full access is not needed
- Review token permissions and grant only what's necessary
- Consider using fine-grained personal access tokens for better security

---

**Last Updated:** January 2026
