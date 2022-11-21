# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG OWNER=jupyter
ARG BASE_CONTAINER=$OWNER/base-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# Install all OS dependencies for fully functional notebook server
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    # Common useful utilities
    git \
    nano\
    tzdata \
    unzip \
    vim-tiny \
    wget \
    curl \
    # git-over-ssh
    openssh-client \
    # less is needed to run help in R
    # see: https://github.com/jupyter/docker-stacks/issues/1588
    less \
    # nbconvert dependencies
    # https://nbconvert.readthedocs.io/en/latest/install.html#installing-tex
    texlive-xetex \
    texlive-fonts-recommended \
    texlive-plain-generic \
    # Enable clipboard on Linux host systems
    xclip && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Create alternative for nano -> nano-tiny
RUN update-alternatives --install /usr/bin/nano nano /bin/nano-tiny 10

USER root

RUN apt-get update --yes && \
    apt install --yes --no-install-recommends \
    wget \
    curl \
    default-jre

ARG OPENREFINE_VERSION=3.6.2
ARG OPENREFINE_PATH=/opt/openrefine
RUN wget https://github.com/OpenRefine/OpenRefine/releases/download/${OPENREFINE_VERSION}/openrefine-linux-${OPENREFINE_VERSION}.tar.gz && \
    mkdir "${OPENREFINE_PATH}" && \
    tar xzf openrefine-linux-${OPENREFINE_VERSION}.tar.gz -C "${OPENREFINE_PATH}" --strip 1

RUN pip install \
    git+https://github.com/innovationOUtside/nb_serverproxy_openrefine.git@main

USER ${NB_UID}
ENV PATH="${OPENREFINE_PATH}:${PATH}"

# Switch back to jovyan to avoid accidental container runs as root
USER ${NB_UID}

# Add R mimetype option to specify how the plot returns from R to the browser
COPY --chown=${NB_UID}:${NB_GID} Rprofile.site /opt/conda/lib/R/etc/
