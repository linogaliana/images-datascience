ARG BASE_IMAGE=ubuntu:20.04
FROM $BASE_IMAGE

# System-wide configuration
SHELL ["/bin/bash", "-c"]
ENV SHELL="/bin/bash"
ENV DEBIAN_FRONTEND="noninteractive"

# Setup user & workspace
ENV USERNAME="onyxia"
ENV UID="1000"
ENV GROUPNAME="users"
ENV GID="100"
ENV HOME="/home/${USERNAME}"
ENV WORKSPACE_DIR="${HOME}/work"
RUN groupmod -g ${GID} ${GROUPNAME} && \
    # Setup custom user with sudo rights
    useradd ${USERNAME} --uid=${UID} -g ${GROUPNAME} --groups sudo -r --no-log-init --create-home && \
    passwd -d ${USERNAME} && \
    # Create workspace
    mkdir ${WORKSPACE_DIR} && \
    # Disable default sudo message when opening shell
    touch ${HOME}/.sudo_as_admin_successful
WORKDIR ${WORKSPACE_DIR}

USER root

ADD common-scripts /opt/

RUN chmod +x -R /opt/ && \
    # Install system libraries
    /opt/install-system-libs.sh && \
    # Upgrade installed system libraries
    apt-get upgrade -y && \
    ## Install common binaries required for Onyxia
    # kubectl
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && \
    chmod +x ./kubectl && \
    mv ./kubectl /usr/local/bin/kubectl && \
    echo 'source <(kubectl completion bash)' >> /home/${USERNAME}/.bashrc && \
    # helm
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 && \
    chmod +x get_helm.sh && \
    ./get_helm.sh && \
    echo 'source <(helm completion bash)' >> /home/${USERNAME}/.bashrc && \
    # mc
    wget -q https://dl.min.io/client/mc/release/linux-amd64/mc -O /usr/local/bin/mc && \
    chmod +x /usr/local/bin/mc && \
    # vault
    VAULT_LATEST_VERSION=$(curl --silent "https://api.github.com/repos/hashicorp/vault/releases/latest" | grep -Po '"tag_name": "v\K.*?(?=")') && \
    wget -q https://releases.hashicorp.com/vault/${VAULT_LATEST_VERSION}/vault_${VAULT_LATEST_VERSION}_linux_amd64.zip -O vault.zip  && \
    unzip vault.zip -d /usr/local/bin/ && \
    vault -autocomplete-install && \
    # Fix permissions
    chown -R ${USERNAME}:${GROUPNAME} ${HOME} && \
    # Clean
    rm get_helm.sh vault.zip && \
    rm -rf /var/lib/apt/lists/*

USER ${USERNAME}

CMD ["/bin/bash"]
