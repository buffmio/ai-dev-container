FROM mcr.microsoft.com/devcontainers/base:bookworm

ARG DEBIAN_FRONTEND=noninteractive

ENV TZ=Asia/Singapore \
    LANG=zh_SG.UTF-8 \
    LANGUAGE=zh_SG:zh_CN:zh \
    LC_ALL=zh_SG.UTF-8 \
    CLAUDE_CONFIG_DIR=/home/vscode/.claude \
    PATH=/home/vscode/.npm-global/bin:${PATH}

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        bind9-dnsutils \
        fd-find \
        file \
        gh \
        iputils-ping \
        python3 \
        python3-pip \
        python3-venv \
        ripgrep \
        shellcheck \
        sqlite3 \
        zstd \
    && ln -snf /usr/bin/fdfind /usr/local/bin/fd \
    && sed -i 's/^# *zh_SG.UTF-8 UTF-8/zh_SG.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen zh_SG.UTF-8 \
    && ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo "${TZ}" > /etc/timezone \
    && curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN install -d -o vscode -g vscode \
        /home/vscode/.npm-global \
        /home/vscode/.npm-cache \
        /home/vscode/.config \
        /home/vscode/.persist \
        /home/vscode/.persist/.claude \
        /home/vscode/.persist/.codex \
        /home/vscode/.persist/.config \
        /home/vscode/.persist/.config/git \
    && install -d -m 0700 -o vscode -g vscode \
        /home/vscode/.persist/.config/gh \
        /home/vscode/.persist/.ssh \
    && install -m 0644 -o vscode -g vscode /dev/null /home/vscode/.persist/.gitconfig \
    && printf '%s\n' \
        'prefix=/home/vscode/.npm-global' \
        'cache=/home/vscode/.npm-cache' \
        'update-notifier=false' \
        'fund=false' \
        'audit=false' \
        > /home/vscode/.npmrc \
    && chown vscode:vscode /home/vscode/.npmrc \
    && rm -rf \
        /home/vscode/.claude \
        /home/vscode/.codex \
        /home/vscode/.ssh \
        /home/vscode/.gitconfig \
        /home/vscode/.config/gh \
        /home/vscode/.config/git \
    && ln -s /home/vscode/.persist/.claude /home/vscode/.claude \
    && ln -s /home/vscode/.persist/.codex /home/vscode/.codex \
    && ln -s /home/vscode/.persist/.ssh /home/vscode/.ssh \
    && ln -s /home/vscode/.persist/.gitconfig /home/vscode/.gitconfig \
    && ln -s /home/vscode/.persist/.config/gh /home/vscode/.config/gh \
    && ln -s /home/vscode/.persist/.config/git /home/vscode/.config/git \
    && chown -h vscode:vscode \
        /home/vscode/.claude \
        /home/vscode/.codex \
        /home/vscode/.ssh \
        /home/vscode/.gitconfig \
        /home/vscode/.config/gh \
        /home/vscode/.config/git

USER vscode

RUN npm install -g \
        "@anthropic-ai/claude-code@latest" \
        "@openai/codex@latest"
