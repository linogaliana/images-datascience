ARG BASE_IMAGE=inseefrlab/onyxia-base
FROM $BASE_IMAGE

SHELL ["/bin/bash", "-c"]

ARG PYTHON_VERSION="3.9"

ENV MAMBA_DIR="/opt/mamba"
ENV PATH="${MAMBA_DIR}/bin:${PATH}"

USER root

RUN wget -q https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh -O mambaforge.sh && \
    # Install mambaforge latest version
    /bin/bash mambaforge.sh -b -p "${MAMBA_DIR}" && \
    # Set specified Python version in base Conda env
    mamba install python=="${PYTHON_VERSION}" && \
    # Activate custom Conda env by default in shell
    echo ". ${MAMBA_DIR}/etc/profile.d/conda.sh && conda activate" >> ${HOME}/.bashrc && \
    # Fix permissions
    chown -R ${USERNAME}:${GROUPNAME} ${HOME} ${MAMBA_DIR} && \
    # Clean
    rm mambaforge.sh && \ 
    mamba clean --all -f -y

USER ${USERNAME}

CMD ["python3"]
