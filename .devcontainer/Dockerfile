FROM mcr.microsoft.com/devcontainers/base:bookworm

ARG DEBIAN_FRONTEND=noninteractive

ENV TZ=Asia/Shanghai \
    LANG=zh_CN.UTF-8 \
    LANGUAGE=zh_CN:zh \
    LC_ALL=zh_CN.UTF-8 \
    CLAUDE_CONFIG_DIR=/home/vscode/.persist/claude \
    CODEX_HOME=/home/vscode/.persist/codex \
    GH_CONFIG_DIR=/home/vscode/.persist/gh \
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
    && sed -i 's/^# *zh_CN.UTF-8 UTF-8/zh_CN.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen zh_CN.UTF-8 \
    && ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo "${TZ}" > /etc/timezone \
    && curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN install -d -o vscode -g vscode \
        /home/vscode/.npm-global \
        /home/vscode/.npm-cache \
        /home/vscode/.persist \
        /home/vscode/.persist/claude \
        /home/vscode/.persist/codex \
        /home/vscode/.persist/git \
        /home/vscode/.persist/git/xdg \
    && install -d -m 0700 -o vscode -g vscode \
        /home/vscode/.persist/gh \
        /home/vscode/.persist/ssh \
    && printf '%s\n' \
        'prefix=/home/vscode/.npm-global' \
        'cache=/home/vscode/.npm-cache' \
        'update-notifier=false' \
        'fund=false' \
        'audit=false' \
        > /home/vscode/.npmrc \
    && chown vscode:vscode /home/vscode/.npmrc \
    && install -d -o vscode -g vscode /home/vscode/.config \
    && rm -rf /home/vscode/.ssh /home/vscode/.gitconfig /home/vscode/.config/git \
    && ln -s /home/vscode/.persist/ssh /home/vscode/.ssh \
    && ln -s /home/vscode/.persist/git/.gitconfig /home/vscode/.gitconfig \
    && ln -s /home/vscode/.persist/git/xdg /home/vscode/.config/git \
    && chown -h vscode:vscode /home/vscode/.ssh /home/vscode/.gitconfig /home/vscode/.config/git

USER vscode

RUN npm install -g \
        "@anthropic-ai/claude-code@latest" \
        "@openai/codex@latest"
