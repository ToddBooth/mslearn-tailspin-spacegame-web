I'm running github codespace and have some errors

How to fix this error and i want one step at a time, to manually debug this via commands

The .NET Core SDK cannot be located. .NET Core debugging will not be enabled. Make sure the .NET Core SDK is installed and is on the path.

# file Dockerfile

# FROM mcr.microsoft.com/devcontainers/dotnet:6.0
FROM docker pull mcr.microsoft.com/dotnet/sdk:6.0-jammy

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

# file: creation.log

# how to fix these github codespace errors?

2025-03-09 15:43:59.325Z: Host information
2025-03-09 15:43:59.325Z: ----------------
2025-03-09 15:43:59.325Z: OS: Ubuntu 22.04.5 LTS (stable release)
2025-03-09 15:43:59.325Z: Image details: https://github.com/github/codespaces-host-images/blob/main/README.md
2025-03-09 15:43:59.326Z: ----------------

=================================================================================
2025-03-09 15:43:59.326Z: Configuration starting...
2025-03-09 15:43:59.355Z: Cloning...

=================================================================================
2025-03-09 15:44:01.960Z: Creating container...
2025-03-09 15:44:02.036Z: $ devcontainer up --id-label Type=codespaces --workspace-folder /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web --mount type=bind,source=/.codespaces/agent/mount/cache,target=/vscode --user-data-folder /var/lib/docker/codespacemount/.persistedshare --container-data-folder .vscode-remote/data/Machine --container-system-data-folder /var/vscode-remote --log-level trace --log-format json --update-remote-user-uid-default never --mount-workspace-git-root false --omit-config-remote-env-from-metadata --skip-non-blocking-commands --skip-post-create --config "/var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer/devcontainer.json" --override-config /root/.codespaces/shared/merged_devcontainer.json --default-user-env-probe loginInteractiveShell --container-session-data-folder /workspaces/.codespaces/.persistedshare/devcontainers-cli/cache --secrets-file /root/.codespaces/shared/user-secrets-envs.json
2025-03-09 15:44:02.328Z: @devcontainers/cli 0.68.0. Node.js v18.20.6. linux 6.5.0-1025-azure x64.
2025-03-09 15:44:04.330Z: Resolving Feature dependencies for 'terraform'...
2025-03-09 15:44:05.429Z: Resolving Feature dependencies for 'azure-cli'...
2025-03-09 15:44:06.220Z: Resolving Feature dependencies for 'git-lfs'...
2025-03-09 15:44:07.170Z: Resolving Feature dependencies for 'github-cli'...
2025-03-09 15:44:08.460Z: Resolving Feature dependencies for 'powershell'...
2025-03-09 15:44:09.250Z: Soft-dependency 'ghcr.io/devcontainers/features/common-utils' is not required.  Removing from installation order...
2025-03-09 15:44:09.252Z: Soft-dependency 'ghcr.io/devcontainers/features/common-utils' is not required.  Removing from installation order...
2025-03-09 15:44:09.253Z: Soft-dependency 'ghcr.io/devcontainers/features/common-utils' is not required.  Removing from installation order...
2025-03-09 15:44:09.254Z: Soft-dependency 'ghcr.io/devcontainers/features/git' is not required.  Removing from installation order...
2025-03-09 15:44:09.255Z: Soft-dependency 'ghcr.io/devcontainers/features/common-utils' is not required.  Removing from installation order...
2025-03-09 15:44:09.256Z: Soft-dependency 'ghcr.io/devcontainers/features/common-utils' is not required.  Removing from installation order...
2025-03-09 15:44:09.627Z: Files to omit: ''
2025-03-09 15:44:10.053Z: Files to omit: ''
2025-03-09 15:44:10.414Z: Files to omit: ''
2025-03-09 15:44:10.784Z: Files to omit: ''
2025-03-09 15:44:11.199Z: Files to omit: ''
2025-03-09 15:44:11.222Z: $ docker buildx build --load --build-arg BUILDKIT_INLINE_CACHE=1 -f /tmp/devcontainercli-root/container-features/0.68.0-1741535044323/Dockerfile-with-features -t vsc-mslearn-tailspin-spacegame-web-42613780e1c4f6c57367b982916b4b4d3ddecc07153e713342c64e7dddd8e4c6 --target dev_containers_target_stage --build-arg UPGRADE_PACKAGES=******** --build-arg ARCH=x64 --build-arg AGENT_VERSION=2.206.1 --build-context dev_containers_feature_content_source=/tmp/devcontainercli-root/container-features/0.68.0-1741535044323 --build-arg _DEV_CONTAINERS_BASE_IMAGE=dev_container_auto_added_stage_label --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer
2025-03-09 15:44:11.997Z: #0 building with "default" instance using docker driver

#1 [internal] load .dockerignore
#1 transferring context: 2B done
#1 DONE 0.1s

#2 [internal] load build definition from Dockerfile-with-features
#2 transferring dockerfile: 8.11kB done
#2 DONE 0.1s

#3 resolve image config for docker.io/docker/dockerfile:1.4
2025-03-09 15:44:12.269Z: #3 ...2025-03-09 15:44:12.270Z: 

#4 [auth] docker/dockerfile:pull token for registry-1.docker.io
#4 DONE 0.0s
2025-03-09 15:44:12.419Z: 
2025-03-09 15:44:12.419Z: #3 resolve image config for docker.io/docker/dockerfile:1.4
2025-03-09 15:44:12.979Z: #3 DONE 1.1s
2025-03-09 15:44:13.087Z: 
#5 docker-image://docker.io/docker/dockerfile:1.4@sha256:9ba7531bd80fb0a858632727cf7a112fbfd19b17e94c4e84ced81e24ef1a0dbc
#5 resolve docker.io/docker/dockerfile:1.4@sha256:9ba7531bd80fb0a858632727cf7a112fbfd19b17e94c4e84ced81e24ef1a0dbc done
#5 sha256:9ba7531bd80fb0a858632727cf7a112fbfd19b17e94c4e84ced81e24ef1a0dbc 2.00kB / 2.00kB done2025-03-09 15:44:13.087Z: 
#5 sha256:ad87fb03593d1b71f9a1cfc1406c4aafcb253b1dabebf569768d6e6166836f34 528B / 528B done
#5 sha256:1e8a16826fd1c80a63fa6817a9c7284c94e40cded14a9b0d0d3722356efa47bd 2.37kB / 2.37kB done
#5 sha256:1328b32c40fca9bcf9d70d8eccb72eb873d1124d72dadce04db8badbe7b08546 0B / 9.94MB 0.1s
2025-03-09 15:44:13.193Z: #5 sha256:1328b32c40fca9bcf9d70d8eccb72eb873d1124d72dadce04db8badbe7b08546 9.44MB / 9.94MB 0.2s
2025-03-09 15:44:13.348Z: #5 sha256:1328b32c40fca9bcf9d70d8eccb72eb873d1124d72dadce04db8badbe7b08546 9.94MB / 9.94MB 0.3s done
#5 extracting sha256:1328b32c40fca9bcf9d70d8eccb72eb873d1124d72dadce04db8badbe7b08546
2025-03-09 15:44:13.951Z: #5 extracting sha256:1328b32c40fca9bcf9d70d8eccb72eb873d1124d72dadce04db8badbe7b08546 0.5s done
#5 DONE 1.0s
2025-03-09 15:44:14.415Z: Dockerfile-with-features:4
--------------------
   2 |     ARG _DEV_CONTAINERS_BASE_IMAGE=placeholder
   3 |     # FROM mcr.microsoft.com/devcontainers/dotnet:6.0
   4 | >>> FROM docker AS dev_container_auto_added_stage_label pull mcr.microsoft.com/dotnet/sdk:6.0-jammy
   5 |     
   6 |     # Install NodeJS
--------------------
ERROR: failed to solve: dockerfile parse error on line 4: FROM requires either one or three arguments2025-03-09 15:44:14.415Z: 
2025-03-09 15:44:14.419Z: Stop: Run: docker buildx build --load --build-arg BUILDKIT_INLINE_CACHE=1 -f /tmp/devcontainercli-root/container-features/0.68.0-1741535044323/Dockerfile-with-features -t vsc-mslearn-tailspin-spacegame-web-42613780e1c4f6c57367b982916b4b4d3ddecc07153e713342c64e7dddd8e4c6 --target dev_containers_target_stage --build-arg UPGRADE_PACKAGES=******** --build-arg ARCH=x64 --build-arg AGENT_VERSION=2.206.1 --build-context dev_containers_feature_content_source=/tmp/devcontainercli-root/container-features/0.68.0-1741535044323 --build-arg _DEV_CONTAINERS_BASE_IMAGE=dev_container_auto_added_stage_label --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer
2025-03-09 15:44:14.419Z: Error: Command failed: docker buildx build --load --build-arg BUILDKIT_INLINE_CACHE=1 -f /tmp/devcontainercli-root/container-features/0.68.0-1741535044323/Dockerfile-with-features -t vsc-mslearn-tailspin-spacegame-web-42613780e1c4f6c57367b982916b4b4d3ddecc07153e713342c64e7dddd8e4c6 --target dev_containers_target_stage --build-arg UPGRADE_PACKAGES=true --build-arg ARCH=x64 --build-arg AGENT_VERSION=2.206.1 --build-context dev_containers_feature_content_source=/tmp/devcontainercli-root/container-features/0.68.0-1741535044323 --build-arg _DEV_CONTAINERS_BASE_IMAGE=dev_container_auto_added_stage_label --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer
2025-03-09 15:44:14.419Z:     at FtA (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:466:1933)
2025-03-09 15:44:14.420Z:     at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
2025-03-09 15:44:14.420Z:     at async Pm (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:465:1896)
2025-03-09 15:44:14.420Z:     at async NH (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:465:610)
2025-03-09 15:44:14.421Z:     at async YtA (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:482:3771)
2025-03-09 15:44:14.422Z: {"outcome":"error","message":"Command failed: docker buildx build --load --build-arg BUILDKIT_INLINE_CACHE=1 -f /tmp/devcontainercli-root/container-features/0.68.0-1741535044323/Dockerfile-with-features -t vsc-mslearn-tailspin-spacegame-web-42613780e1c4f6c57367b982916b4b4d3ddecc07153e713342c64e7dddd8e4c6 --target dev_containers_target_stage --build-arg UPGRADE_PACKAGES=true --build-arg ARCH=x64 --build-arg AGENT_VERSION=2.206.1 --build-context dev_containers_feature_content_source=/tmp/devcontainercli-root/container-features/0.68.0-1741535044323 --build-arg _DEV_CONTAINERS_BASE_IMAGE=dev_container_auto_added_stage_label --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer","description":"An error occurred building the image."}
2025-03-09 15:44:14.426Z:     at async eB (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:482:4886)
2025-03-09 15:44:14.426Z:     at async prA (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:663:200)
2025-03-09 15:44:14.426Z:     at async drA (/.codespaces/agent/bin/node_modules/@devcontainers/cli/dist/spec-node/devContainersSpecCLI.js:662:14706)
2025-03-09 15:44:14.434Z: devcontainer process exited with exit code 1

====================================== ERROR ====================================
2025-03-09 15:44:14.443Z: Failed to create container.
=================================================================================
2025-03-09 15:44:14.443Z: Error: Command failed: docker buildx build --load --build-arg BUILDKIT_INLINE_CACHE=1 -f /tmp/devcontainercli-root/container-features/0.68.0-1741535044323/Dockerfile-with-features -t vsc-mslearn-tailspin-spacegame-web-42613780e1c4f6c57367b982916b4b4d3ddecc07153e713342c64e7dddd8e4c6 --target dev_containers_target_stage --build-arg UPGRADE_PACKAGES=true --build-arg ARCH=x64 --build-arg AGENT_VERSION=2.206.1 --build-context dev_containers_feature_content_source=/tmp/devcontainercli-root/container-features/0.68.0-1741535044323 --build-arg _DEV_CONTAINERS_BASE_IMAGE=dev_container_auto_added_stage_label --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer
2025-03-09 15:44:14.447Z: Error code: 1302 (UnifiedContainersErrorFatalCreatingContainer)

====================================== ERROR ====================================
2025-03-09 15:44:14.455Z: Container creation failed.
=================================================================================
2025-03-09 15:44:14.466Z: 

===================================== WARNING ===================================
2025-03-09 15:44:14.467Z: Creating recovery container.
=================================================================================

=================================================================================
2025-03-09 15:45:15.223Z: Creating container...
2025-03-09 15:45:15.248Z: $ devcontainer up --id-label Type=codespaces --workspace-folder /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web --mount type=bind,source=/.codespaces/agent/mount/cache,target=/vscode --user-data-folder /var/lib/docker/codespacemount/.persistedshare --container-data-folder .vscode-remote/data/Machine --container-system-data-folder /var/vscode-remote --log-level trace --log-format json --update-remote-user-uid-default never --mount-workspace-git-root false --omit-config-remote-env-from-metadata --skip-non-blocking-commands --skip-post-create --config "/var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer/devcontainer.json" --override-config /root/.codespaces/shared/merged_devcontainer.json --default-user-env-probe loginInteractiveShell --container-session-data-folder /workspaces/.codespaces/.persistedshare/devcontainers-cli/cache --secrets-file /root/.codespaces/shared/user-secrets-envs.json
2025-03-09 15:45:15.413Z: @devcontainers/cli 0.68.0. Node.js v18.20.6. linux 6.5.0-1025-azure x64.
2025-03-09 15:45:15.787Z: $alpine -c echo Container started
2025-03-09 15:45:15.805Z: Unable to find image 'mcr.microsoft.com/devcontainers/base:alpine' locally
2025-03-09 15:45:15.913Z: alpine: Pulling from devcontainers/base
2025-03-09 15:45:15.956Z: 
[1A[2K2025-03-09 15:45:15.957Z: 
da9db072f522: Pulling fs layer 
[1B
[1A[2K
3cdff9e33f37: Pulling fs layer 
[1B
[1A[2K
87dffdaee0b6: Pulling fs layer 
[1B
[1A[2K
3f72e2f1b3f3: Pulling fs layer 
[1B
[1A[2K
1ac829ce5af3: Pulling fs layer 
[1B
[1A[2K
2a15ab263bdf: Pulling fs layer 
[1B
[1A[2K
a9bdcaa5c876: Pulling fs layer 
[1B2025-03-09 15:45:15.958Z: [4A2025-03-09 15:45:15.959Z: [2K2025-03-09 15:45:15.960Z: 
2025-03-09 15:45:15.960Z: 3f72e2f1b3f3: 2025-03-09 15:45:15.960Z: Waiting 
2025-03-09 15:45:15.960Z: [4B2025-03-09 15:45:15.961Z: [3A2025-03-09 15:45:15.961Z: [2K2025-03-09 15:45:15.961Z: 
2025-03-09 15:45:15.962Z: 1ac829ce5af3: 2025-03-09 15:45:15.963Z: Waiting 
2025-03-09 15:45:15.963Z: [3B2025-03-09 15:45:15.964Z: [2A2025-03-09 15:45:15.965Z: [2K2025-03-09 15:45:15.965Z: 
2025-03-09 15:45:15.965Z: 2a15ab263bdf: 2025-03-09 15:45:15.965Z: Waiting 
2025-03-09 15:45:15.966Z: [2B2025-03-09 15:45:15.966Z: [1A2025-03-09 15:45:15.966Z: [2K2025-03-09 15:45:15.966Z: 
2025-03-09 15:45:15.967Z: a9bdcaa5c876: 2025-03-09 15:45:15.967Z: Waiting 
2025-03-09 15:45:15.967Z: [1B2025-03-09 15:45:16.007Z: [6A[2K
3cdff9e33f37: 2025-03-09 15:45:16.007Z: Downloading     410B/410B
[6B[6A[2K
3cdff9e33f37: Verifying Checksum 
[6B[6A[2K
3cdff9e33f37: Download complete 
[6B2025-03-09 15:45:16.016Z: [5A[2K
87dffdaee0b6: Downloading     135B/135B
[5B2025-03-09 15:45:16.018Z: [5A[2K
2025-03-09 15:45:16.018Z: 87dffdaee0b6: Verifying Checksum 
[5B[5A[2K
87dffdaee0b6: Download complete 
[5B2025-03-09 15:45:16.036Z: [7A[2K
da9db072f522: Downloading  48.34kB/3.624MB
[7B2025-03-09 15:45:16.054Z: [4A[2K
2025-03-09 15:45:16.054Z: 3f72e2f1b3f3: Downloading     223B/223B
[4B2025-03-09 15:45:16.056Z: [4A[2K
3f72e2f1b3f3: 2025-03-09 15:45:16.056Z: Verifying Checksum 
[4B[4A[2K
3f72e2f1b3f3: Download complete 
[4B2025-03-09 15:45:16.062Z: [7A[2K
da9db072f522: 2025-03-09 15:45:16.062Z: Downloading  3.624MB/3.624MB
[7B[7A[2K
da9db072f522: Verifying Checksum 
[7B[7A[2K
da9db072f522: Download complete 
[7B2025-03-09 15:45:16.076Z: [3A[2K
1ac829ce5af3: Downloading     235B/235B
[3B[3A[2K
1ac829ce5af3: Verifying Checksum 
[3B[3A[2K
1ac829ce5af3: Download complete 
[3B[7A[2K
da9db072f522: Extracting  65.54kB/3.624MB
[7B2025-03-09 15:45:16.116Z: [2A2025-03-09 15:45:16.117Z: [2K2025-03-09 15:45:16.117Z: 
2025-03-09 15:45:16.118Z: 2a15ab263bdf: 2025-03-09 15:45:16.118Z: Downloading  538.9kB/230.6MB
2025-03-09 15:45:16.119Z: [2B2025-03-09 15:45:16.151Z: [1A[2K
a9bdcaa5c876: Downloading  440.6kB/43.41MB
[1B2025-03-09 15:45:16.185Z: [7A[2K
da9db072f522: 2025-03-09 15:45:16.187Z: Extracting  1.507MB/3.624MB
[7B2025-03-09 15:45:16.217Z: [2A2025-03-09 15:45:16.217Z: [2K2025-03-09 15:45:16.218Z: 
2025-03-09 15:45:16.218Z: 2a15ab263bdf: 2025-03-09 15:45:16.218Z: Downloading  23.77MB/230.6MB
2025-03-09 15:45:16.219Z: [2B2025-03-09 15:45:16.251Z: [1A2025-03-09 15:45:16.252Z: [2K2025-03-09 15:45:16.252Z: 
2025-03-09 15:45:16.253Z: a9bdcaa5c876: 2025-03-09 15:45:16.253Z: Downloading  21.23MB/43.41MB
2025-03-09 15:45:16.253Z: [1B2025-03-09 15:45:16.291Z: [7A2025-03-09 15:45:16.292Z: [2K2025-03-09 15:45:16.292Z: 
2025-03-09 15:45:16.292Z: da9db072f522: 2025-03-09 15:45:16.293Z: Extracting  2.032MB/3.624MB
2025-03-09 15:45:16.293Z: [7B2025-03-09 15:45:16.325Z: [2A2025-03-09 15:45:16.326Z: [2K2025-03-09 15:45:16.326Z: 
2025-03-09 15:45:16.327Z: 2a15ab263bdf: 2025-03-09 15:45:16.328Z: Downloading  46.48MB/230.6MB
2025-03-09 15:45:16.329Z: [2B2025-03-09 15:45:16.351Z: [1A2025-03-09 15:45:16.353Z: [2K2025-03-09 15:45:16.354Z: 
2025-03-09 15:45:16.356Z: a9bdcaa5c876: 2025-03-09 15:45:16.357Z: Downloading  42.02MB/43.41MB
[1B2025-03-09 15:45:16.362Z: [1A[2K
a9bdcaa5c876: Verifying Checksum 
[1B2025-03-09 15:45:16.362Z: [1A[2K
a9bdcaa5c876: Download complete 
[1B2025-03-09 15:45:16.392Z: [7A[2K
da9db072f522: Extracting  3.539MB/3.624MB
[7B2025-03-09 15:45:16.406Z: [7A[2K
da9db072f522: Extracting  3.624MB/3.624MB
[7B2025-03-09 15:45:16.420Z: [2A[2K
2a15ab263bdf: Downloading   74.6MB/230.6MB
[2B2025-03-09 15:45:16.448Z: [7A2025-03-09 15:45:16.449Z: [2K2025-03-09 15:45:16.450Z: 
2025-03-09 15:45:16.451Z: da9db072f522: 2025-03-09 15:45:16.454Z: Pull complete 
2025-03-09 15:45:16.455Z: [7B2025-03-09 15:45:16.465Z: [6A[2K
3cdff9e33f37: Extracting     410B/410B
[6B[6A[2K
3cdff9e33f37: 2025-03-09 15:45:16.465Z: Extracting     410B/410B
[6B2025-03-09 15:45:16.530Z: [2A2025-03-09 15:45:16.530Z: [2K2025-03-09 15:45:16.530Z: 
2025-03-09 15:45:16.530Z: 2a15ab263bdf: 2025-03-09 15:45:16.530Z: Downloading    100MB/230.6MB
2025-03-09 15:45:16.530Z: [2B2025-03-09 15:45:16.634Z: [2A[2K
2a15ab263bdf: Downloading  121.1MB/230.6MB
[2B2025-03-09 15:45:16.724Z: [2A[2K
2a15ab263bdf: Downloading  142.7MB/230.6MB
[2B2025-03-09 15:45:16.825Z: [2A2025-03-09 15:45:16.826Z: [2K
2a15ab263bdf: Downloading  170.8MB/230.6MB
[2B2025-03-09 15:45:16.926Z: [2A2025-03-09 15:45:16.927Z: [2K
2a15ab263bdf: Downloading  196.8MB/230.6MB
[2B2025-03-09 15:45:17.029Z: [2A2025-03-09 15:45:17.029Z: [2K
2a15ab263bdf: Downloading    220MB/230.6MB
[2B2025-03-09 15:45:17.070Z: [2A[2K
2a15ab263bdf: Verifying Checksum 
[2B2025-03-09 15:45:17.071Z: [2A[2K
2a15ab263bdf: Download complete 
[2B2025-03-09 15:45:18.021Z: [6A[2K
3cdff9e33f37: Pull complete 
[6B2025-03-09 15:45:18.027Z: [5A2025-03-09 15:45:18.027Z: [2K
87dffdaee0b6: Extracting     135B/135B
[5B[5A[2K2025-03-09 15:45:18.027Z: 
87dffdaee0b6: Extracting     135B/135B
[5B2025-03-09 15:45:18.866Z: [5A[2K
87dffdaee0b6: Pull complete 
[5B2025-03-09 15:45:18.869Z: [4A2025-03-09 15:45:18.870Z: [2K
3f72e2f1b3f3: Extracting     223B/223B
2025-03-09 15:45:18.870Z: [4B[4A[2K
3f72e2f1b3f3: Extracting     223B/223B
[4B2025-03-09 15:45:18.884Z: [4A2025-03-09 15:45:18.884Z: [2K
3f72e2f1b3f3: Pull complete 
[4B2025-03-09 15:45:18.886Z: [3A2025-03-09 15:45:18.886Z: [2K
1ac829ce5af3: Extracting     235B/235B
[3B[3A[2K
1ac829ce5af3: Extracting     235B/235B
[3B2025-03-09 15:45:18.899Z: [3A2025-03-09 15:45:18.901Z: [2K
1ac829ce5af3: Pull complete 
[3B2025-03-09 15:45:18.914Z: [2A2025-03-09 15:45:18.915Z: [2K
2a15ab263bdf: Extracting  557.1kB/230.6MB
[2B2025-03-09 15:45:19.016Z: [2A[2K
2a15ab263bdf: Extracting  5.571MB/230.6MB
[2B2025-03-09 15:45:19.127Z: [2A[2K
2a15ab263bdf: Extracting  12.81MB/230.6MB
[2B2025-03-09 15:45:19.226Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:19.227Z: Extracting  18.94MB/230.6MB
[2B2025-03-09 15:45:19.351Z: [2A[2K
2a15ab263bdf: Extracting  23.95MB/230.6MB
2025-03-09 15:45:19.351Z: [2B2025-03-09 15:45:19.478Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:19.478Z: Extracting  25.07MB/230.6MB
[2B2025-03-09 15:45:19.592Z: [2A[2K
2a15ab263bdf: Extracting  26.18MB/230.6MB
[2B2025-03-09 15:45:19.695Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:19.695Z: Extracting  29.52MB/230.6MB
[2B2025-03-09 15:45:19.802Z: [2A[2K
2025-03-09 15:45:19.802Z: 2a15ab263bdf: Extracting  32.31MB/230.6MB
[2B2025-03-09 15:45:19.909Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:19.909Z: Extracting  40.67MB/230.6MB
[2B2025-03-09 15:45:20.018Z: [2A[2K
2a15ab263bdf: Extracting  47.35MB/230.6MB
[2B2025-03-09 15:45:20.122Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:20.122Z: Extracting  52.92MB/230.6MB
[2B2025-03-09 15:45:20.228Z: [2A[2K
2a15ab263bdf: Extracting  57.38MB/230.6MB
[2B2025-03-09 15:45:20.347Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:20.367Z: Extracting  61.28MB/230.6MB
[2B2025-03-09 15:45:20.447Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:20.448Z: Extracting   75.2MB/230.6MB
[2B2025-03-09 15:45:20.551Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:20.551Z: Extracting  90.24MB/230.6MB
[2B2025-03-09 15:45:20.652Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:20.653Z: Extracting  104.7MB/230.6MB
[2B2025-03-09 15:45:20.761Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:20.761Z: Extracting  113.6MB/230.6MB
[2B2025-03-09 15:45:20.864Z: [2A[2K
2025-03-09 15:45:20.866Z: 2a15ab263bdf: Extracting  117.5MB/230.6MB
[2B2025-03-09 15:45:20.986Z: [2A[2K
2a15ab263bdf: Extracting  120.3MB/230.6MB
[2B2025-03-09 15:45:21.087Z: [2A[2K
2a15ab263bdf: Extracting  122.6MB/230.6MB
2025-03-09 15:45:21.088Z: [2B2025-03-09 15:45:21.192Z: [2A2025-03-09 15:45:21.193Z: [2K2025-03-09 15:45:21.193Z: 
2025-03-09 15:45:21.193Z: 2a15ab263bdf: 2025-03-09 15:45:21.193Z: Extracting  130.9MB/230.6MB
2025-03-09 15:45:21.194Z: [2B2025-03-09 15:45:21.293Z: [2A2025-03-09 15:45:21.293Z: [2K
2a15ab263bdf: Extracting  138.1MB/230.6MB
[2B2025-03-09 15:45:21.399Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:21.400Z: Extracting  146.5MB/230.6MB
[2B2025-03-09 15:45:21.506Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:21.507Z: Extracting  153.2MB/230.6MB
[2B2025-03-09 15:45:21.611Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:21.612Z: Extracting    161MB/230.6MB
[2B2025-03-09 15:45:21.716Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:21.717Z: Extracting  168.2MB/230.6MB
[2B2025-03-09 15:45:21.820Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:21.820Z: Extracting    176MB/230.6MB
[2B2025-03-09 15:45:21.929Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:21.929Z: Extracting  183.8MB/230.6MB
[2B2025-03-09 15:45:22.031Z: [2A[2K
2025-03-09 15:45:22.031Z: 2a15ab263bdf: Extracting  191.6MB/230.6MB
[2B2025-03-09 15:45:22.134Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:22.135Z: Extracting  198.9MB/230.6MB
[2B2025-03-09 15:45:22.236Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:22.237Z: Extracting  207.2MB/230.6MB
[2B2025-03-09 15:45:22.342Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:22.342Z: Extracting    215MB/230.6MB
[2B2025-03-09 15:45:22.483Z: [2A[2K
2025-03-09 15:45:22.484Z: 2a15ab263bdf: Extracting  221.7MB/230.6MB
[2B2025-03-09 15:45:22.593Z: [2A[2K
2a15ab263bdf: Extracting  223.4MB/230.6MB
[2B2025-03-09 15:45:22.701Z: [2A[2K
2a15ab263bdf: 2025-03-09 15:45:22.701Z: Extracting  225.6MB/230.6MB
[2B2025-03-09 15:45:22.841Z: [2A[2K
2a15ab263bdf: Extracting  226.7MB/230.6MB
[2B2025-03-09 15:45:23.023Z: [2A[2K
2a15ab263bdf: Extracting  227.8MB/230.6MB
[2B2025-03-09 15:45:23.158Z: [2A[2K
2a15ab263bdf: Extracting  228.4MB/230.6MB
[2B2025-03-09 15:45:23.169Z: [2A[2K
2a15ab263bdf: Extracting  230.6MB/230.6MB
[2B2025-03-09 15:45:27.403Z: [2A[2K
2a15ab263bdf: Pull complete 
[2B2025-03-09 15:45:27.436Z: [1A2025-03-09 15:45:27.436Z: [2K
a9bdcaa5c876: Extracting  458.8kB/43.41MB
[1B2025-03-09 15:45:27.591Z: [1A[2K
a9bdcaa5c876: Extracting  5.046MB/43.41MB
[1B2025-03-09 15:45:27.701Z: [1A[2K
a9bdcaa5c876: Extracting  8.716MB/43.41MB
[1B2025-03-09 15:45:27.873Z: [1A[2K
a9bdcaa5c876: Extracting  12.85MB/43.41MB
[1B2025-03-09 15:45:27.975Z: [1A[2K
a9bdcaa5c876: Extracting  18.81MB/43.41MB
[1B2025-03-09 15:45:28.076Z: [1A[2K
a9bdcaa5c876: Extracting  25.23MB/43.41MB
[1B2025-03-09 15:45:28.187Z: [1A[2K
a9bdcaa5c876: Extracting  29.36MB/43.41MB
[1B2025-03-09 15:45:28.309Z: [1A[2K
a9bdcaa5c876: Extracting  30.28MB/43.41MB
[1B2025-03-09 15:45:28.430Z: [1A[2K
a9bdcaa5c876: Extracting   31.2MB/43.41MB
[1B2025-03-09 15:45:28.534Z: [1A2025-03-09 15:45:28.535Z: [2K
a9bdcaa5c876: Extracting  32.11MB/43.41MB
[1B2025-03-09 15:45:28.636Z: [1A[2K
a9bdcaa5c876: Extracting  34.41MB/43.41MB
[1B2025-03-09 15:45:28.772Z: [1A[2K
a9bdcaa5c876: Extracting   36.7MB/43.41MB
[1B2025-03-09 15:45:28.900Z: [1A[2K
a9bdcaa5c876: Extracting  39.91MB/43.41MB
[1B2025-03-09 15:45:29.072Z: [1A[2K
a9bdcaa5c876: Extracting  41.29MB/43.41MB
[1B2025-03-09 15:45:29.641Z: [1A[2K2025-03-09 15:45:29.642Z: 
a9bdcaa5c876: 2025-03-09 15:45:29.642Z: Extracting  43.41MB/43.41MB
[1B2025-03-09 15:45:30.682Z: [1A[2K
a9bdcaa5c876: Pull complete 
[1B2025-03-09 15:45:30.686Z: Digest: sha256:5212d8ed44c89bfadad14a03104ef75b09c5de8642a58721c271f2e9155f5023
2025-03-09 15:45:30.689Z: Status: Downloaded newer image for mcr.microsoft.com/devcontainers/base:alpine
2025-03-09 15:45:30.923Z: Container started
2025-03-09 15:45:31.122Z: Outcome: success User: vscode WorkspaceFolder: /workspaces/mslearn-tailspin-spacegame-web
2025-03-09 15:45:31.132Z: devcontainer process exited with exit code 0

=================================================================================
2025-03-09 15:45:31.507Z: Running blocking commands...
2025-03-09 15:45:31.536Z: $ devcontainer up --id-label Type=codespaces --workspace-folder /var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web --mount type=bind,source=/.codespaces/agent/mount/cache,target=/vscode --user-data-folder /var/lib/docker/codespacemount/.persistedshare --container-data-folder .vscode-remote/data/Machine --container-system-data-folder /var/vscode-remote --log-level trace --log-format json --update-remote-user-uid-default never --mount-workspace-git-root false --omit-config-remote-env-from-metadata --skip-non-blocking-commands --expect-existing-container --config "/var/lib/docker/codespacemount/workspace/mslearn-tailspin-spacegame-web/.devcontainer/devcontainer.json" --override-config /root/.codespaces/shared/merged_devcontainer.json --default-user-env-probe loginInteractiveShell --container-session-data-folder /workspaces/.codespaces/.persistedshare/devcontainers-cli/cache --secrets-file /root/.codespaces/shared/user-secrets-envs.json
2025-03-09 15:45:31.814Z: @devcontainers/cli 0.68.0. Node.js v18.20.6. linux 6.5.0-1025-azure x64.
2025-03-09 15:45:32.099Z: Outcome: success User: vscode WorkspaceFolder: /workspaces/mslearn-tailspin-spacegame-web
2025-03-09 15:45:32.160Z: devcontainer process exited with exit code 0

=================================================================================
2025-03-09 15:45:32.216Z: Configuring codespace...

=================================================================================
2025-03-09 15:45:32.216Z: Finished configuring codespace.

