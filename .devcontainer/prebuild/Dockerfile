# syntax=docker/dockerfile:1
FROM mcr.microsoft.com/devcontainers/javascript-node:24-bookworm

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        git openssh-client ca-certificates curl wget jq ripgrep fd-find fzf bat \
        tree less nano htop tmux zip unzip xz-utils file lsof procps iproute2 \
        dnsutils netcat-openbsd build-essential python3 pkg-config \
    && rm -rf /var/lib/apt/lists/*

ENV VP_HOME=/home/node/.vite-plus \
    NPM_CONFIG_PREFIX=/home/node/.local
ENV PATH="/home/node/.local/bin:/home/node/.vite-plus/bin:${PATH}"

USER node
ARG PNPM_VERSION=12.16.1
ARG TSRS_VERSION=0.20.1-ts7.1.0-beta
ARG VP_VERSION=1.4.0

RUN mkdir -p "$NPM_CONFIG_PREFIX/bin" \
    && ln -sf /usr/bin/fdfind "$NPM_CONFIG_PREFIX/bin/fd" \
    && ln -sf /usr/bin/batcat "$NPM_CONFIG_PREFIX/bin/bat" \
    && npm install --global "pnpm@$PNPM_VERSION" "tsrs@$TSRS_VERSION"

RUN set -eu; \
    installer_path="$(mktemp)"; \
    trap 'rm -f "$installer_path"' EXIT; \
    curl --fail --silent --show-error --location https://vite.plus --output "$installer_path"; \
    CI=1 VP_VERSION="$VP_VERSION" VP_NODE_MANAGER=no VP_PM_MANAGER=no bash "$installer_path"; \
    "$VP_HOME/bin/vp" env off

# Fail the image build if any of the installed tools cannot start.
RUN node --version \
    && pnpm --version \
    && vp --version \
    && tsrs --version \
    && test -x /home/node/.local/lib/node_modules/tsrs/tsdk/tsgo
