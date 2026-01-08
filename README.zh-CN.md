# Cline MCP 集成指南

使用 Docker 设置 GitHub MCP 服务器与 Cline 的完整指南。

[English](README.md) | 简体中文

## 什么是 MCP？

模型上下文协议（MCP）是一个开放标准，使 Cline 等 AI 助手能够安全地连接到外部数据源和工具。MCP 服务器通过工具和资源提供额外的功能，扩展 Cline 的能力。

## 目录

- [前置要求](#前置要求)
- [GitHub MCP 服务器](#github-mcp-服务器)
  - [设置步骤](#设置步骤)
  - [配置](#配置)
  - [功能特性](#功能特性)
- [故障排除](#故障排除)
- [其他资源](#其他资源)

## 前置要求

在设置 GitHub MCP 服务器之前，请确保您已安装：

- **Cline** - 已在 Visual Studio Code 中安装
- **Docker** - 已安装并正在运行
- **GitHub 个人访问令牌** - 具有适当的权限

## GitHub MCP 服务器

GitHub MCP 服务器使 Cline 能够与 GitHub 仓库、议题（issues）、拉取请求（pull requests）等进行交互。

### 设置步骤

1. **创建 GitHub 个人访问令牌**

   - 访问 https://github.com/settings/tokens
   - 点击"Generate new token"（生成新令牌）（classic）
   - 授予适当的权限：
     - `repo` - 完全控制私有仓库
     - `read:org` - 读取组织和团队成员资格
     - `read:user` - 读取用户配置文件数据
     - `user:email` - 访问用户电子邮件地址
   - 复制生成的令牌

2. **配置 Cline MCP 设置**

   - 打开 VS Code 设置（Ctrl/Cmd + ,）
   - 搜索"Cline MCP"
   - 点击"Edit in settings.json"（在 settings.json 中编辑）
   - 添加 GitHub MCP 服务器配置（见下文）

3. **更新环境变量**
   - 将配置中的 `xxx` 替换为您的 GitHub 个人访问令牌
   - 将 `GITHUB_READ_ONLY` 设置为 `true`（只读访问）或 `false`（完全访问）

### 配置

将此配置添加到您的 Cline MCP 设置中：

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

**配置参数说明：**

- `command`: 使用 Docker 运行 GitHub MCP 服务器容器
- `args`: Docker 运行参数，包括环境变量传递
- `env.GITHUB_PERSONAL_ACCESS_TOKEN`: 您的 GitHub 个人访问令牌
- `env.GITHUB_READ_ONLY`: 设置为 `"true"` 启用只读模式，`"false"` 启用完全访问
- `disabled`: 设置为 `false` 启用服务器，`true` 禁用服务器

**使用系统环境变量：**

您也可以引用系统环境变量：

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

### 功能特性

GitHub MCP 服务器提供以下功能：

**仓库管理：**

- 搜索仓库
- 获取仓库内容
- 创建和管理分支
- Fork 仓库

**议题管理：**

- 列出和搜索议题
- 创建新议题
- 更新议题详情
- 为议题添加评论
- 管理议题标签和分配人

**拉取请求操作：**

- 列出和搜索拉取请求
- 创建新的拉取请求
- 审查拉取请求
- 添加审查评论
- 合并拉取请求
- 更新拉取请求分支

**提交操作：**

- 查看提交历史
- 获取提交详情
- 通过提交创建和更新文件

**代码操作：**

- 跨仓库搜索代码
- 读取文件内容
- 创建或更新文件
- 删除文件

**组织与团队：**

- 列出团队
- 获取团队成员

**其他功能：**

- 请求 Copilot 代码审查
- 将 Copilot 分配给议题
- 管理发布版本和标签
- 使用 GitHub Actions

## 故障排除

### 常见问题

**Docker 未运行：**

- 确保 Docker Desktop 已安装并正在运行
- 验证 Docker 是否可访问：`docker --version`
- 检查 Docker 守护进程是否处于活动状态

**身份验证错误：**

- 验证您的 GitHub 令牌是否有效且未过期
- 确保令牌具有所需的作用域/权限
- 检查令牌是否在配置中正确设置
- 尝试重新生成您的 GitHub 个人访问令牌

**容器拉取问题：**

- 确保您有互联网连接
- 检查 Docker 是否可以拉取镜像：`docker pull ghcr.io/github/github-mcp-server`
- 验证公司防火墙没有阻止 GitHub Container Registry

**服务器无法启动：**

- 查看 VS Code 开发者工具（帮助 > 切换开发者工具）中的错误消息
- 检查 Cline 输出面板中的 MCP 服务器日志
- 验证配置 JSON 是否有效（无语法错误）

**权限被拒绝：**

- 确保您的 GitHub 令牌具有您尝试执行的操作所需的权限
- 如果使用只读模式，请验证操作是否为只读
- 检查仓库访问权限

### 调试

在 Cline 设置中启用 MCP 日志记录：

```json
{
  "cline.mcpDebug": true
}
```

在 VS Code 输出面板中查看日志（视图 > 输出 > Cline）

### 测试设置

配置完成后，重启 VS Code 并验证 GitHub MCP 服务器已连接：

1. 打开 Cline
2. GitHub MCP 工具应该可用
3. 尝试一个简单的命令，如列出您的仓库

### 获取帮助

- [Cline 文档](https://docs.cline.bot)
- [MCP 文档](https://modelcontextprotocol.io)
- [GitHub MCP 服务器仓库](https://github.com/github/github-mcp-server)
- [Cline GitHub 议题](https://github.com/cline/cline/issues)

## 其他资源

- [GitHub 个人访问令牌文档](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [Docker 文档](https://docs.docker.com/)
- [MCP 官方规范](https://spec.modelcontextprotocol.io)
- [GitHub REST API 文档](https://docs.github.com/zh/rest)

## 安全最佳实践

- **永远不要将您的 GitHub 令牌提交到版本控制**
- 使用环境变量或 VS Code 设置存储敏感数据
- 定期轮换您的 GitHub 个人访问令牌
- 在不需要完全访问权限时使用只读模式
- 审查令牌权限，只授予必要的权限
- 考虑使用细粒度个人访问令牌以获得更好的安全性

---

**最后更新：** 2026 年 1 月
