# Multi-Language Development Environment
# Based on Ubuntu 22.04 LTS for stability and wide package support
FROM ubuntu:22.04

# Prevent interactive prompts during package installation
ARG DEBIAN_FRONTEND=noninteractive

# Set timezone
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Update system and install essential build tools and dependencies
RUN apt-get update && apt-get install -y \
    # Essential build tools
    build-essential \
    cmake \
    make \
    autoconf \
    automake \
    libtool \
    pkg-config \
    # System utilities
    curl \
    wget \
    git \
    vim \
    nano \
    unzip \
    zip \
    tar \
    gzip \
    # SSL and security
    ca-certificates \
    gnupg \
    lsb-release \
    # Development libraries
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    libffi-dev \
    liblzma-dev \
    libncurses5-dev \
    libncursesw5-dev \
    # Ruby dependencies
    libyaml-dev \
    libgdbm-dev \
    libgdbm-compat-dev \
    # Python development
    python3 \
    python3-dev \
    python3-pip \
    python3-venv \
    ripgrep \ 
    # Additional utilities
    software-properties-common \
    apt-transport-https \
    && rm -rf /var/lib/apt/lists/*

# Install C/C++ tools (already included in build-essential, but adding specific versions)
RUN apt-get update && apt-get install -y \
    gcc-11 \
    g++-11 \
    clang \
    gdb \
    valgrind \
    && rm -rf /var/lib/apt/lists/*

# Set default C/C++ compiler versions
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 100 && \
    update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 100

# Install Node.js and npm via NodeSource repository
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
    apt-get install -y nodejs

# Install Yarn package manager
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor -o /usr/share/keyrings/yarn-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/yarn-keyring.gpg] https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install -y yarn && \
    rm -rf /var/lib/apt/lists/*

# Install Go
ENV GO_VERSION=1.21.5
RUN wget https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz && \
    tar -C /usr/local -xzf go${GO_VERSION}.linux-amd64.tar.gz && \
    rm go${GO_VERSION}.linux-amd64.tar.gz

# Install Rust via rustup
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH="/root/.cargo/bin:$PATH"

# Install Ruby via rbenv
RUN git clone https://github.com/rbenv/rbenv.git /root/.rbenv && \
    git clone https://github.com/rbenv/ruby-build.git /root/.rbenv/plugins/ruby-build
ENV PATH="/root/.rbenv/bin:$PATH"
RUN echo 'eval "$(rbenv init -)"' >> /root/.bashrc

# Install latest stable Ruby with proper dependencies
ENV RUBY_VERSION=3.2.0
ENV RUBY_CONFIGURE_OPTS="--with-openssl-dir=/usr --disable-install-doc"
RUN /root/.rbenv/bin/rbenv install $RUBY_VERSION && \
    /root/.rbenv/bin/rbenv global $RUBY_VERSION && \
    /root/.rbenv/bin/rbenv rehash

# Install Python version manager (pyenv)
RUN git clone https://github.com/pyenv/pyenv.git /root/.pyenv
ENV PATH="/root/.pyenv/bin:$PATH"
RUN echo 'export PYENV_ROOT="/root/.pyenv"' >> /root/.bashrc && \
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> /root/.bashrc && \
    echo 'eval "$(pyenv init -)"' >> /root/.bashrc

# Install Node Version Manager (nvm)
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
ENV NVM_DIR="/root/.nvm"
RUN . "$NVM_DIR/nvm.sh" && nvm install --lts

# Set up environment variables
ENV PATH="/usr/local/go/bin:$PATH"
ENV PATH="/root/.rbenv/shims:/root/.rbenv/bin:$PATH"
ENV PATH="/root/.cargo/bin:$PATH"
ENV GOPATH="/root/go"
ENV PATH="$GOPATH/bin:$PATH"

# Install common package managers and tools
RUN pip3 install --upgrade pip setuptools wheel && \
    pip3 install pipenv poetry virtualenv

# Install bundler for Ruby
RUN /root/.rbenv/shims/gem install bundler

# Install pnpm for Node.js
RUN npm install -g pnpm

# Install common development tools
RUN npm install -g \
    typescript \
    eslint \
    prettier \
    nodemon \
    pm2

# Install Rust tools
RUN cargo install cargo-watch cargo-edit

# Create workspace directory
RUN mkdir -p /workspace
WORKDIR /workspace

# Set up shell configuration
RUN echo 'export PATH="/usr/local/go/bin:$PATH"' >> /root/.bashrc && \
    echo 'export PATH="/root/.cargo/bin:$PATH"' >> /root/.bashrc && \
    echo 'export GOPATH="/root/go"' >> /root/.bashrc && \
    echo 'export PATH="$GOPATH/bin:$PATH"' >> /root/.bashrc && \
    echo 'export NVM_DIR="/root/.nvm"' >> /root/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> /root/.bashrc && \
    echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' >> /root/.bashrc

# Create a script to display installed versions
RUN echo '#!/bin/bash\n\
echo "=== Development Environment Versions ==="\n\
echo "C/C++:"\n\
gcc --version | head -1\n\
g++ --version | head -1\n\
echo ""\n\
echo "Python:"\n\
python3 --version\n\
pip3 --version\n\
echo ""\n\
echo "Node.js/JavaScript:"\n\
node --version\n\
npm --version\n\
yarn --version\n\
pnpm --version\n\
echo ""\n\
echo "Go:"\n\
go version\n\
echo ""\n\
echo "Rust:"\n\
rustc --version\n\
cargo --version\n\
echo ""\n\
echo "Ruby:"\n\
/root/.rbenv/shims/ruby --version\n\
/root/.rbenv/shims/gem --version\n\
/root/.rbenv/shims/bundler --version\n\
echo ""\n\
echo "Build tools:"\n\
make --version | head -1\n\
cmake --version | head -1\n\
git --version\n\
' > /usr/local/bin/versions && chmod +x /usr/local/bin/versions

# OpenAI codex
RUN npm install -g @openai/codex

# Claude code
RUN npm install -g @anthropic-ai/claude-code

# Set default shell
SHELL ["/bin/bash", "-c"]

# Default command
CMD ["/bin/bash"]

# Labels for documentation
LABEL maintainer="Development Team"
LABEL description="Multi-language development environment for C/C++, Python, Ruby, Go, Rust, and JavaScript/Node.js"
LABEL version="1.0"