ARG VARIANT="noble"
FROM buildpack-deps:${VARIANT}-curl

# LABEL dev.containers.features="common"

ARG VARIANT
RUN if [ "$VARIANT" = "noble" ]; then \
        if id "ubuntu" &>/dev/null; then \
            echo "Deleting user 'ubuntu'  for $VARIANT" && userdel -f -r ubuntu || echo "Failed to delete ubuntu user for $VARIANT"; \  
        else \
            echo "User 'ubuntu' does not exist for $VARIANT"; \ 
        fi; \
    fi

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends git


ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # Add sudo support
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && rm -rf /var/lib/apt/lists/*
USER $USERNAME

COPY ./py.sh .

RUN export VERSION="3.12" && export USERNAME="vscode" && sudo chmod +777 ./py.sh && sudo ./py.sh

ENV PYTHON_PATH="/usr/local/python/current"
ENV PIPX_HOME="/usr/local/py-utils"
ENV PIPX_BIN_DIR="/usr/local/py-utils/bin"
ENV PATH="/usr/local/python/current/bin:/usr/local/py-utils/bin:${PATH}"

USER vscode
# Debug /usr/local/python
RUN echo "PYTHON_SRC: $(which python3)" \
    && echo "PYTHON3_VERSION: $(python3 --version)" \
    && echo "PYTHON_VERSION: $(python --version)" \
    && echo "PIP_VERSION: $(pip3 --version)" \
    && echo "PIPX_VERSION: $(pipx --version)"

RUN pip install ipywidgets 
