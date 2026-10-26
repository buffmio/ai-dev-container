# AI Dev Container

[English README](README.md)

**AI Dev Container** 是一个可复用的 VS Code Dev Container 模板，面向 AI 辅助开发。它提供基于 Debian Bookworm 的开发环境，预装 Claude Code、OpenAI Codex CLI、Node.js 22、Git、GitHub CLI、Docker CLI，并持久化保存开发凭据，同时提供 Agent 沙箱工具所需的权限配置。

## 功能

- Claude Code CLI 及 Anthropic VS Code 扩展
- OpenAI Codex CLI 及 `openai.chatgpt` VS Code 扩展
- Node.js 22、Git、OpenSSH、ripgrep、fd、jq、tree、rsync、make、sqlite3、Python 3（含 `pip` 和 `venv`）、GitHub CLI 和 ShellCheck
- Docker CE CLI、Docker Compose 和 Buildx
- 简体中文 UTF-8 环境，时区为 `Asia/Shanghai`
- 非 root 的 `vscode` 开发用户
- Claude Code、Codex、Git、GitHub CLI 和 SSH 配置持久化

## 前置要求

- Docker Engine 或 Docker Desktop
- Visual Studio Code
- VS Code Dev Containers 扩展

本模板在容器内独立维护 Git 用户信息、SSH 密钥和 GitHub CLI 登录状态，并保存到持久化 Docker volume，不依赖宿主机 Git 配置或 SSH Agent。

## 使用

```bash
git clone git@github.com:buffmio/ai-dev-container.git
cd ai-dev-container
code .
```

在 VS Code 命令面板中执行：

```text
Dev Containers: Reopen in Container
```

首次启动需要构建镜像和下载 Dev Container Feature，耗时取决于网络情况。

修改 `.devcontainer/devcontainer.json` 或 `.devcontainer/Dockerfile` 后，执行：

```text
Dev Containers: Rebuild Container
```

## 初始化 Claude Code、Codex 和 GitHub CLI

进入开发容器后运行：

```bash
claude
codex
gh auth login
gh auth setup-git
```

根据终端提示完成登录。Claude Code、Codex 和 GitHub CLI 的相关配置保存在持久化 Docker volume 中，重建容器后仍会保留。

## 配置 Git 和 SSH

请直接在容器内配置 Git 身份并生成 SSH 密钥：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
ssh-keygen -t ed25519 -C "你的邮箱"
```

查看公钥并添加到 GitHub、GitLab 或其他代码托管平台：

```bash
cat ~/.ssh/id_ed25519.pub
```

Git 配置和 SSH 密钥位于挂载到 `/home/vscode/.persist` 的 Docker volume。删除该 volume 会同时删除这些持久化凭据，请在删除前妥善备份私钥。

## Docker 使用方式

本模板使用 Docker-outside-of-Docker（DooD）：把宿主机 Docker socket 提供给开发容器使用，不会在开发容器中启动第二个 Docker daemon。

验证 Docker 访问：

```bash
docker version
docker compose version
docker buildx version
```

通过 socket 创建的容器、镜像、网络和卷都属于宿主机 Docker daemon，与宿主机共享。

工作区在容器内使用与宿主机相同的绝对路径映射，因此通过 `$(pwd)` 生成 Docker bind mount 时，宿主机 Docker daemon 能够解析到正确路径。路径包含空格或非 ASCII 字符时，请对路径加引号。

> [!WARNING]
> 能访问 Docker socket 的进程基本拥有宿主机 root 级别的控制能力。请只在可信代码和可信工作区中使用本模板。

## Agent 沙箱

开发容器启用了 `CAP_SYS_ADMIN`、`seccomp=unconfined` 和 `systempaths=unconfined`，用于允许 Claude Code 和 Codex 使用 `bubblewrap` 创建子进程沙箱。`CAP_SYS_ADMIN` 用于命名空间和挂载，`systempaths=unconfined` 用于解除 Docker 对 `/proc` 等系统路径的 masked/read-only 限制。

这些设置会降低开发容器自身的隔离强度，但容器没有启用 `privileged`。由于本模板同时使用 DooD，Docker socket 仍然是通往宿主机的高权限入口。请不要将本模板当作不可信代码的强隔离环境。

## 持久化内容

命名 Docker volume 挂载到：

```text
/home/vscode/.persist
```

其中保存：

- Claude Code 配置
- Codex 配置
- GitHub CLI 配置和登录状态
- Git 全局配置
- SSH 密钥和配置

项目源代码由 VS Code 挂载，不保存在该用户状态 volume 中。

GitHub CLI 配置目录为 `/home/vscode/.persist/gh`，其中可能包含 GitHub Token，请妥善保护该 Docker volume。删除该 volume 会同时删除这些持久化认证信息。

## 常用检查

```bash
claude --version
codex --version
node --version
npm --version
git --version
gh --version
shellcheck --version
docker version
```

如果 `docker version` 提示权限不足，请先确认宿主机 Docker daemon 正在运行，然后执行 `Dev Containers: Rebuild Container`，让官方 Docker-outside-of-Docker Feature 重新配置 socket 访问权限。

## 版本更新

- Claude Code CLI 在重建镜像时安装当时可用的 `latest` 版本。
- Codex CLI 在重建镜像时安装当时可用的 `latest` 版本。
- GitHub CLI 和 ShellCheck 由 Debian 软件源提供。
- 开发容器不预装完整项目编译工具链；项目编译应在 GitHub Actions 或项目自己的容器中完成。
- Docker CLI、Compose 和 Buildx 由官方 `docker-outside-of-docker:1` Feature 提供。
- 本仓库作为通用模板，不提交 `devcontainer-lock.json`；不同时间首次构建可能解析到不同的兼容 Feature 版本。
