FROM fpco/stack-build:lts-11

# Install gettext, contains the envsubst command, for env vars substitution
# Also install python3 and Jinja2, needed for Kubernetes templating
# As well as libyaml for pyaml and jinja2-cli for cli jinja tools
RUN apt-get install -y gettext python3-pip libyaml-dev
RUN pip3 install Jinja2
RUN pip3 install PyYAML
RUN pip3 install jinja2-cli

# Install dhall and various utilities
RUN stack install dhall dhall-json dhall-text

# Install Google Cloud SDK
# Code from here: https://github.com/GoogleCloudPlatform/cloud-sdk-docker/blob/master/debian_slim/Dockerfile
ENV CLOUD_SDK_VERSION 198.0.0
ARG INSTALL_COMPONENTS
RUN apt-get update -qqy && apt-get install -qqy \
        curl \
        gcc \
        python-dev \
        python-setuptools \
        apt-transport-https \
        lsb-release \
        openssh-client \
        git \
    && easy_install -U pip && \
    pip install -U crcmod && \
    export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)" && \
    echo "deb https://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" > /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - && \
    apt-get update && apt-get install -y google-cloud-sdk=${CLOUD_SDK_VERSION}-0 $INSTALL_COMPONENTS && \
    gcloud config set core/disable_usage_reporting true && \
    gcloud config set component_manager/disable_update_check true && \
    gcloud config set metrics/environment github_docker_image && \
    gcloud --version
VOLUME ["/root/.config"]

# Install docker
RUN apt-get install -qqy \
      curl \
      apt-transport-https \
      ca-certificates \
      software-properties-common
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
RUN sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable"
RUN apt-get update -qqy && apt-get install -qqy docker-ce
