# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG OWNER=jupyter
ARG BASE_CONTAINER=$OWNER/base-notebook
FROM $BASE_CONTAINER

LABEL maintainer="OULIB"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# Install all OS dependencies for fully functional notebook server
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    # Common useful utilities
    git \
    nano \
    tzdata \
    unzip \
    vim-tiny \
    wget \
    curl \
    default-jre \
    # git-over-ssh
    openssh-client \
    # less is needed to run help in R
    # see: https://github.com/jupyter/docker-stacks/issues/1588
    less \
    # nbconvert dependencies
    # https://nbconvert.readthedocs.io/en/latest/install.html#installing-tex
    #texlive-xetex \
    #texlive-fonts-recommended \
    #texlive-plain-generic \
    # Enable clipboard on Linux host systems
    #xclip && \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

ARG OPENREFINE_VERSION=3.6.2
ARG OPENREFINE_PATH=/opt/openrefine
RUN wget "https://oss.sonatype.org/service/local/artifact/maven/content?r=releases&g=org.openrefine&a=openrefine&v=3.6.2&c=linux&p=tar.gz" -O openrefine.tar.gz && \
    mkdir "${OPENREFINE_PATH}" && \
    tar -xzf openrefine.tar.gz -C "${OPENREFINE_PATH}" --strip 1

RUN pip install \
    git+https://gitlab.nrp-nautilus.io/SHBurris/nb_serverproxy_openrefine.git@main

# Switch back to jovyan to avoid accidental container runs as root
USER ${NB_UID}
ENV PATH="${OPENREFINE_PATH}:${PATH}"
