# FROM mcr.microsoft.com/devcontainers/dotnet:6.0
FROM mcr.microsoft.com/dotnet/sdk:6.0-ubuntu

# Install NodeJS
# [Choice] Node.js version: none, lts/*, 18, 16, 14
ARG NODE_VERSION="16"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

# Todd's change 5
RUN apt-get update  

# Install Gulp
RUN npm install --global gulp-cli

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# [Optional] Install zsh 
ARG INSTALL_ZSH="true"
# [Optional] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/
RUN bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true"

# cd into the user directory, download and unzip the Azure DevOps agent
# RUN cd /home/vscode && mkdir azure-pipelines && cd azure-pipelines
RUN cd /home/vscode 
RUN mkdir azure-pipelines 
RUN cd azure-pipelines

# input Azure DevOps agent arguments
ARG ARCH="x64"
ARG AGENT_VERSION="2.206.1"

# RUN cd /home/vscode/azure-pipelines \
#     && curl -O -L https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz \
#     && tar xzf /home/vscode/azure-pipelines/vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz \
#     && /home/vscode/azure-pipelines/bin/installdependencies.sh

RUN set -ex; \
    echo "Checking current directory and listing /home/vscode:"; \
    pwd; \
    ls -la /home/vscode; \
    echo "Creating azure-pipelines directory if it doesn't exist"; \
    mkdir -p /home/vscode/azure-pipelines; \
    echo "Listing /home/vscode/azure-pipelines:"; \
    ls -la /home/vscode/azure-pipelines; \
    echo "Changing directory to /home/vscode/azure-pipelines"; \
    cd /home/vscode/azure-pipelines || { echo "ERROR: Cannot change to /home/vscode/azure-pipelines"; exit 1; }; \
    echo "Current directory:"; \
    pwd; \
    echo "Downloading Azure DevOps Agent version ${AGENT_VERSION} for ${ARCH} architecture..."; \
    curl -v -O -L https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz; \
    echo "Download completed. Listing downloaded file:"; \
    ls -lh vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz; \
    echo "Extracting the downloaded archive..."; \
    tar -xzf vsts-agent-linux-${ARCH}-${AGENT_VERSION}.tar.gz; \
    echo "Extraction completed. Listing contents:"; \
    ls -la; \
    echo "Running dependency installation script..."; \
    /home/vscode/azure-pipelines/bin/installdependencies.sh; \
    echo "Dependency installation finished."


# copy over the start.sh script
COPY library-scripts/start.sh /home/vscode/azure-pipelines/start.sh

# Apply ownership of home folder
RUN chown -R vscode ~vscode

# make the script executable
RUN chmod +x /home/vscode/azure-pipelines/start.sh

# Clean up
RUN rm -rf /var/lib/apt/lists/* /tmp/library-scripts
