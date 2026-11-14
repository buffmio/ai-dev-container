FROM mcr.microsoft.com/devcontainers/base:bookworm

ARG DEBIAN_FRONTEND=noninteractive

ENV TZ=Asia/Singapore \
    LANG=zh_SG.UTF-8 \
    LANGUAGE=zh_SG:zh_CN:zh \
    LC_ALL=zh_SG.UTF-8 \
    CLAUDE_CONFIG_DIR=/home/vscode/.claude \
    PATH=/home/vscode/.npm-global/bin:${PATH}

RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y --no-install-recommends \
        bind9-dnsutils \
        fd-find \
        file \
        gh \
        iputils-ping \
        nodejs \
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
    && rm -rf /var/lib/apt/lists/*

USER vscode

RUN install -d \
        /home/vscode/.npm-global \
        /home/vscode/.npm-cache \
        /home/vscode/.config \
        /home/vscode/.persist/.claude \
        /home/vscode/.persist/.codex \
        /home/vscode/.persist/.config/git \
    && install -d -m 0700 \
        /home/vscode/.persist/.config/gh \
        /home/vscode/.persist/.ssh \
    && install -m 0644 /dev/null /home/vscode/.persist/.gitconfig \
    && printf '%s\n' \
        'prefix=/home/vscode/.npm-global' \
        'cache=/home/vscode/.npm-cache' \
        'update-notifier=false' \
        'fund=false' \
        'audit=false' \
        > /home/vscode/.npmrc \
    && rm -rf /home/vscode/.ssh /home/vscode/.gitconfig \
    && ln -s /home/vscode/.persist/.claude /home/vscode/.claude \
    && ln -s /home/vscode/.persist/.codex /home/vscode/.codex \
    && ln -s /home/vscode/.persist/.ssh /home/vscode/.ssh \
    && ln -s /home/vscode/.persist/.gitconfig /home/vscode/.gitconfig \
    && ln -s /home/vscode/.persist/.config/gh /home/vscode/.config/gh \
    && ln -s /home/vscode/.persist/.config/git /home/vscode/.config/git \
    && npm install -g \
        "@anthropic-ai/claude-code@latest" \
        "@openai/codex@latest"
