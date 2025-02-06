# Environment builder dockerfile
# Creates image for x86 systems, provides python 3.7, 3.8, 3.9, 3.10 
# & node v18.x.x runtimes

# Use a minimal Debian or Ubuntu image
FROM debian:stable-slim


# variables
ARG TARGET_VERSIONS="3.7.17"
ARG BUILD_FOLDER_NAME_PREFIX="buildsystem_"
ARG BUILDER_VENV="builder"


# Install essential packages
RUN apt-get update && apt-get install -y \
    ca-certificates \
    curl \
    git \
    wget \
    build-essential \
    libssl-dev \
    libgdbm-dev \
    libc6-dev \
    libbz2-dev \
    libncurses5-dev \
    libffi-dev \
    liblzma-dev \
    libreadline-dev \
    zlib1g-dev \
    libgdbm-dev \
    libncursesw5-dev \
    libssl-dev \
    libsqlite3-dev \
    tk-dev \
    libgdbm-dev \
    libncursesw5-dev \
    libssl-dev \
    libsqlite3-dev \
    tk-dev \
    python3-dev \
    python3-pip \
    gcc \
    lzma \
    && rm -rf /var/lib/apt/lists/*


# Install Pyenv
RUN curl https://pyenv.run | bash

# Add Pyenv to the PATH
ENV PATH="/root/.pyenv/bin:$PATH"

# Add the following lines to the user's shell configuration
RUN echo "export PATH=\"$HOME/.pyenv/bin:$PATH\"" >> /etc/bash.bashrc
RUN echo "eval \"\$(pyenv init -)\"" >> /etc/bash.bashrc
RUN echo "eval \"\$(pyenv virtualenv-init -)\"" >> /etc/bash.bashrc

# Execute the source command explicitly using a shell
RUN /bin/bash -c "source /etc/bash.bashrc"


# Install multiple Python versions
RUN for version in $(echo "$TARGET_VERSIONS" | tr ',' '\n'); do \
    pyenv install "$version"; \
done


# Create a virtual environment for each Python version with Cython
RUN for version in $(echo "$TARGET_VERSIONS" | tr ',' '\n'); do \
    normalized_version=$(echo $version | sed 's/\./ /g' | cut -d ' ' -f 1,2); \
    clean_version=$(echo "$normalized_version" | tr -d ' '); \
    pyenv virtualenv "$version" "$BUILD_FOLDER_NAME_PREFIX$clean_version"; \    
    "/root/.pyenv/versions/$version/envs/$BUILD_FOLDER_NAME_PREFIX$clean_version/bin/pip3" install --upgrade setuptools wheel; \
    "/root/.pyenv/versions/$version/envs/$BUILD_FOLDER_NAME_PREFIX$clean_version/bin/pip" install Cython; \
done


# Create environment for builder with boto3, cython & Gitpython
RUN { \
    FIRST_VERSION=$(echo "$TARGET_VERSIONS" | sed 's/, */ /g' | cut -d' ' -f1); \
    pyenv virtualenv "$FIRST_VERSION" "$BUILDER_VENV"; \
    "/root/.pyenv/versions/$FIRST_VERSION/envs/$BUILDER_VENV/bin/pip3" install --upgrade setuptools wheel; \
    "/root/.pyenv/versions/$FIRST_VERSION/envs/$BUILDER_VENV/bin/pip" install boto3 Gitpython Cython; \
}


# Install Node.js v18.17.1
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs

# Verify Node.js and npm versions
RUN node -v && npm -v


# Set working directory
WORKDIR /builder

