# AI Dev Container

[中文说明](README_zh.md)

**AI Dev Container** is a reusable VS Code Dev Container template for AI-assisted development. It provides a Debian Bookworm environment with Claude Code, OpenAI Codex CLI, Node.js 22, Git, GitHub CLI, Docker CLI, persistent credentials, and the permissions required by agent sandboxing tools.

## Features

- Claude Code CLI with the Anthropic VS Code extension
- OpenAI Codex CLI with the `openai.chatgpt` VS Code extension
- Node.js 22, Git, OpenSSH, ripgrep, fd, jq, tree, rsync, make, sqlite3, Python 3 (`pip` and `venv`), GitHub CLI, and ShellCheck
- Docker CE CLI, Docker Compose, and Buildx
- Simplified Chinese UTF-8 locale with the `Asia/Shanghai` time zone
- A non-root `vscode` development user
- Persistent Claude Code, Codex, Git, GitHub CLI, and SSH configuration

## Prerequisites

- Docker Engine or Docker Desktop
- Visual Studio Code
- The VS Code Dev Containers extension

The template keeps Git identity, SSH keys, and GitHub CLI authentication inside the container and stores them in a persistent Docker volume. It does not depend on the host's Git configuration or SSH agent.

## Getting Started

```bash
git clone git@github.com:buffmio/ai-dev-container.git
cd ai-dev-container
code .
```

In the VS Code Command Palette, run:

```text
Dev Containers: Reopen in Container
```

The first startup builds the image and downloads the Dev Container Feature. Build time depends on network speed.

After changing `.devcontainer/devcontainer.json` or `.devcontainer/Dockerfile`, run:

```text
Dev Containers: Rebuild Container
```

## Initialize Claude Code, Codex, and GitHub CLI

Inside the development container, run:

```bash
claude
codex
gh auth login
gh auth setup-git
```

Follow the prompts to complete each login. Claude Code, Codex, and GitHub CLI configuration is stored in the persistent Docker volume and survives container rebuilds.

## Configure Git and SSH

Configure your Git identity and generate an SSH key inside the container:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
ssh-keygen -t ed25519 -C "you@example.com"
```

Print the public key and add it to GitHub, GitLab, or another code hosting platform:

```bash
cat ~/.ssh/id_ed25519.pub
```

Git configuration and SSH keys are stored in the Docker volume mounted at `/home/vscode/.persist`. Deleting the volume also deletes these persisted credentials. Back up private keys securely before removing the volume.

## Docker Access

This template uses Docker-outside-of-Docker (DooD): the host Docker socket is exposed to the development container, and no second Docker daemon is started inside it.

Verify Docker access with:

```bash
docker version
docker compose version
docker buildx version
```

Containers, images, networks, and volumes created through the socket belong to the host Docker daemon and are shared with the host.

The workspace is mounted at the same absolute path inside and outside the container. This allows bind mounts generated with `$(pwd)` to resolve correctly on the host Docker daemon. Quote paths that contain spaces or non-ASCII characters.

> [!WARNING]
> A process with access to the Docker socket effectively has root-level control over the host. Use this template only with trusted code and trusted workspaces.

## Agent Sandboxing

The development container enables `CAP_SYS_ADMIN`, `seccomp=unconfined`, and `systempaths=unconfined` so Claude Code and Codex can use `bubblewrap` to create subprocess sandboxes. `CAP_SYS_ADMIN` supports namespaces and mounts, while `systempaths=unconfined` removes Docker's masked or read-only restrictions on system paths such as `/proc`.

These settings reduce the isolation of the development container itself, although the container is not run with `privileged`. Because the template also exposes the Docker socket, the socket remains the higher-privilege path to the host. Do not treat this template as a strong isolation boundary for untrusted code.

## Persisted Data

The named Docker volume is mounted at:

```text
/home/vscode/.persist
```

It stores:

- Claude Code configuration
- Codex configuration
- GitHub CLI configuration and authentication state
- Global Git configuration
- SSH keys and configuration

Project source code is mounted by VS Code and is not stored in this user-state volume.

The GitHub CLI configuration directory is `/home/vscode/.persist/gh` and may contain a GitHub token. Protect this Docker volume carefully. Deleting it also deletes the persisted authentication data.

## Common Checks

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

If `docker version` reports a permission error, confirm that the host Docker daemon is running and then run `Dev Containers: Rebuild Container` so the official Docker-outside-of-Docker Feature can configure socket access again.

## Version Updates

- The Claude Code CLI installs the `latest` version available when the image is rebuilt.
- The Codex CLI installs the `latest` version available when the image is rebuilt.
- GitHub CLI and ShellCheck are provided by the Debian package repositories.
- The development container does not include a complete project build toolchain; compile projects in GitHub Actions or in the project's own container.
- Docker CLI, Compose, and Buildx are provided by the official `docker-outside-of-docker:1` Feature.
- This repository is a reusable template and intentionally does not commit `devcontainer-lock.json`; compatible Feature versions may resolve differently when the image is built at different times.
