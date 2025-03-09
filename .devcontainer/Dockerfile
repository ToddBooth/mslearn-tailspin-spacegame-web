FROM mcr.microsoft.com/dotnet/sdk:6.0-jammy AS build-env

# Create vscode user
ARG USERNAME=vscode
RUN useradd -ms /bin/bash ${USERNAME}

# Install Node.js 18 quietly
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -qq -y nodejs

# Install required packages quietly
RUN apt-get update -qq \
    && apt-get install -qq -y liblttng-ust1 \
    && rm -rf /var/lib/apt/lists/*

# Install global npm tools quietly 
RUN npm install -g gulp-cli --silent

# Install Azure CLI quietly
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash > /dev/null

# [Optional] Install zsh and upgrade OS packages quietly
ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="false"
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/
RUN bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" > /dev/null

# Download and extract Azure DevOps agent quietly
ARG ARCH="x64"
ARG AGENT_VERSION="2.206.1"

RUN mkdir -p /home/vscode/azure-pipelines \
    && cd /home/vscode/azure-pipelines \
    && curl -sSL https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz \
       | tar xzf - \
    && sed -i 's/liblttng-ust0/liblttng-ust1/g' ./bin/installdependencies.sh \
    && sed -i '/libssl1.0.0/d' ./bin/installdependencies.sh \
    && sed -i '/libssl1.0.2/d' ./bin/installdependencies.sh \
    && sed -i '/libssl1.1/d' ./bin/installdependencies.sh \
    && ./bin/installdependencies.sh > /dev/null

# Copy and set permissions for start script
COPY library-scripts/start.sh /home/vscode/azure-pipelines/start.sh
RUN chown -R vscode:vscode /home/vscode \
    && chmod +x /home/vscode/azure-pipelines/start.sh

# Final cleanup
RUN rm -rf /tmp/library-scripts
