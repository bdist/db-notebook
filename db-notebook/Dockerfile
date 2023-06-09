# Copyright (c) Flavio Martins <flavio.f.martins@tecnico.ulisboa.pt>
# Distributed under the terms of the Modified BSD License.
FROM jupyter/scipy-notebook:2023-05-15

LABEL maintainer="Flavio Martins <flavio.f.martins@tecnico.ulisboa.pt>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# Create the file repository configuration and Import the repository signing key
RUN echo "deb http://apt.postgresql.org/pub/repos/apt jammy-pgdg main" > /etc/apt/sources.list.d/pgdg.list && \
    wget --quiet -O /etc/apt/trusted.gpg.d/pgdg.asc https://www.postgresql.org/media/keys/ACCC4CF8.asc

# Install the latest version of PostgreSQL.
# If you want a specific version, use 'postgresql-15' or similar instead of 'postgresql':
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    # for postgresql-client
    postgresql-client && \
    apt-get clean && rm -rf /var/lib/apt/lists/*


USER ${NB_UID}

# Install Python language server
RUN mamba install --yes \
    'jupyterlab-lsp' \
    'python-lsp-server' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install SQL language server
RUN npm install -g --save-dev \
    sql-language-server && \
    npm cache clean --force && \
    fix-permissions "/home/${NB_USER}"

# Install SQLite
RUN mamba install --yes \
    'sqlite' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install PostgreSQL Python drivers
RUN mamba install --yes \
    'psycopg2' \
    'psycopg2-binary' \
    'pgspecial==2.0.1' \
    'psycopg' \
    'psycopg-c' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install jupysql (pip needed for sqlalchemy 2.0.*)
RUN pip install --quiet --no-cache-dir \
    'jupysql' && \
     fix-permissions "/home/${NB_USER}"

# Install DuckDB engine and library
RUN mamba install --yes \
    'duckdb-engine' \
    'python-duckdb' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

RUN pip install --quiet --no-cache-dir \
    'jupyterlab_execute_time==2.*' && \
     fix-permissions "/home/${NB_USER}"

USER ${NB_UID}

WORKDIR "${HOME}"
