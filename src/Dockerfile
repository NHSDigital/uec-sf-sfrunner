ARG VERSION=latest
ARG OS=debian
ARG CLI_NAME=toolbox

FROM cloudposse/geodesic:$VERSION-$OS

# Install necessary tools
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    gnupg2 wget curl apt-transport-https gpg \
    software-properties-common uidmap \
    libgtk2.0-0 libgtk-3-0 libgbm-dev libnotify-dev libnss3 libxss1 libasound2 libxtst6 xauth xvfb \
    fuse3 slirp4netns dbus-user-session podman podman-compose && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install Java (Temurin JDK)
RUN wget -qO - https://packages.adoptium.net/artifactory/api/gpg/key/public | gpg --dearmor | tee /etc/apt/trusted.gpg.d/adoptium.gpg > /dev/null
RUN echo "deb https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list

RUN apt-get update && \
    apt-get install -y --no-install-recommends temurin-20-jdk && \
    rm -rf /var/lib/apt/lists/*

# Set JAVA_HOME
RUN JAVA_HOME=$(find /usr/lib/jvm/ -name "temurin-20-jdk*" -type d | head -n 1) && \
    echo "export JAVA_HOME=$JAVA_HOME" >> /etc/profile

# Install Node.js
RUN curl -fsSL https://deb.nodesource.com/setup_21.x | bash - && \
    apt-get install -y nodejs && \
    corepack enable

# Install Maven
RUN wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz && \
    tar xzvf apache-maven-3.9.6-bin.tar.gz && \
    mv apache-maven-3.9.6 /opt/ && \
    rm apache-maven-3.9.6-bin.tar.gz

ENV PATH="/opt/apache-maven-3.9.6/bin:$PATH"

# Install Taskfile
RUN cd /; sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d

# Shell banner
ENV BANNER="sfrunner"
ENV MOTD_URL=""

# AWS CLI Configurations
ENV AWS_VAULT_ENABLED=false
ENV AWS_VAULT_SERVER_ENABLED=false
ENV AWS_VAULT_BACKEND=file
ENV AWS_SDK_LOAD_CONFIG=1
ENV AWS_SESSION_TOKEN_TTL=3h
ENV AWS_ASSUME_ROLE_TTL=3h
ENV AWS_FEDERATION_TOKEN_TTL=3h
ENV AWS_DEFAULT_REGION=us-east-1
ENV AWS_DEFAULT_SHORT_REGION=ue1
ENV AWS_REGION_ABBREVIATION_TYPE=fixed

ARG AWS_ACC
ENV DOCKER_IMAGE="${AWS_ACC}.dkr.ecr.eu-west-2.amazonaws.com/uec-sf/sfrunner"
ENV DOCKER_TAG="latest"
ARG SFRUNNER_VER
ENV SFRUNNER_VER="${SFRUNNER_VER}"

WORKDIR /

# Set shell
ENV SHELL=/bin/bash
