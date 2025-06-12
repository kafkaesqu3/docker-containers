# Use official Node.js runtime as base image
FROM node:22-slim

# Install system dependencies for Chrome and sudo
RUN apt-get update && apt-get install -y \
    wget \
    gnupg \
    ca-certificates \
    procps \
    libxss1 \
    fonts-liberation \
    libappindicator3-1 \
    libasound2 \
    libatk-bridge2.0-0 \
    libdrm2 \
    libgtk-3-0 \
    libnspr4 \
    libnss3 \
    libx11-xcb1 \
    libxcomposite1 \
    libxdamage1 \
    libxrandr2 \
    xdg-utils \
    libgbm1 \
    libxkbcommon0 \
    libwayland-client0 \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# Install Google Chrome
RUN wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list \
    && apt-get update \
    && apt-get install -y google-chrome-stable \
    && rm -rf /var/lib/apt/lists/*

# Create app directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install npm dependencies
RUN npm install

# Install the Codex CLI globally
RUN npm install -g @openai/codex
RUN npm install -g slurp-ai

# Create a user to run Chrome (Chrome won't run as root) and add to sudo group
RUN groupadd -r pptruser && useradd -r -g pptruser -G audio,video,sudo pptruser \
    && mkdir -p /home/pptruser/Downloads \
    && chown -R pptruser:pptruser /home/pptruser \
    && chown -R pptruser:pptruser /app \
    && echo 'pptruser ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

# Switch to non-root user
USER pptruser



# Default working directory
WORKDIR /workspace

# Default command
CMD [ "bash" ]
