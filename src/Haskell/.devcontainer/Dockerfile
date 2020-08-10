#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Amaranthos Labs, LLC. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

FROM haskell:8.8 as base
# https://hub.docker.com/_/haskell

# Add a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="true"
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# Add vscode gpg
RUN curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg \
    && install -o root -g root -m 644 microsoft.gpg /usr/share/keyrings/microsoft-archive-keyring.gpg \
    && sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft-archive-keyring.gpg] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'

RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install code

RUN cabal update && mkdir -p /home/vscode/.cabal/bin && echo "user-install: False" >> /home/vscode/.cabal/config

FROM debian
COPY --from=base /usr/local/bin /usr/local/bin
COPY --from=base /usr/local/lib /usr/local/lib
COPY --from=base /usr/local/share /usr/local/share

ENTRYPOINT ["haskell"]
