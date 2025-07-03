# .devcontainer/Dockerfile
# This Dockerfile sets up the Flutter and Android SDKs.

# Use a base image with common development tools
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

# Arguments for versioning, passed from devcontainer.json
ARG FLUTTER_VERSION=3.22.2
ARG ANDROID_CMD_TOOLS_VERSION=11076708

# Set environment variables for Flutter and Android SDKs
ENV FLUTTER_HOME=/opt/flutter
ENV ANDROID_SDK_ROOT=/opt/android
ENV PATH=$FLUTTER_HOME/bin:$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/platform-tools:$PATH

# Install necessary system dependencies
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    openjdk-17-jdk \
    wget \
    unzip \
    libglu1-mesa \
    xz-utils \
    git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Download and install the Flutter SDK
RUN mkdir -p ${FLUTTER_HOME} && \
    wget -qO- https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}-stable.tar.xz | tar -xJ --strip-components=1 -C ${FLUTTER_HOME} && \
    chown -R vscode:vscode ${FLUTTER_HOME}

# Download and install the Android SDK command-line tools
RUN mkdir -p /opt/android/cmdline-tools && \
    wget -qO- https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_CMD_TOOLS_VERSION}_latest.zip > /tmp/cmd-tools.zip && \
    unzip -q /tmp/cmd-tools.zip -d /opt/android/cmdline-tools && \
    mv /opt/android/cmdline-tools/cmdline-tools /opt/android/cmdline-tools/latest && \
    rm /tmp/cmd-tools.zip && \
    chown -R vscode:vscode /opt/android

# Switch to the non-root user to accept licenses and install Android components
USER vscode
WORKDIR /home/vscode

# Accept Android SDK licenses
RUN yes | ${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin/sdkmanager --licenses

# Install essential Android SDK components
RUN ${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin/sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# Pre-download Flutter dependencies
RUN flutter precache

# Set back to root user for container tooling
USER root
