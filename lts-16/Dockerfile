FROM fpco/stack-build:lts-16.18

# Install gettext, contains the envsubst command, for env vars substitution
# Also install python3 and Jinja2, needed for Kubernetes templating
# As well as libyaml for pyaml and jinja2-cli for cli jinja tools
RUN apt-get install -y gettext python3-pip libyaml-dev
RUN pip3 install Jinja2
RUN pip3 install PyYAML
RUN pip3 install jinja2-cli

# Build Haskell dependencies that we typically use
RUN stack build --resolver lts-14.16 \
  safe-exceptions \
  conduit-extra xml-conduit http-conduit \
  http-client \
  warp \
  servant-server servant-client servant-swagger servant-swagger-ui \
  swagger2 \
  lens aeson lens-aeson cassava rio \
  tasty doctest \
  these these-lens semialign \
  haskell-src-exts hpack \
  persistent postgresql-simple persistent-postgresql persistent-template postgresql-libpq

# Install dhall and various utilities
RUN stack install dhall-1.30.0 dhall-json-1.6.2 --resolver=nightly-2020-03-25

# Install docker
RUN apt-get -qqy update
RUN apt-get install -qqy \
      curl \
      apt-transport-https \
      ca-certificates \
      software-properties-common
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
RUN sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable"
RUN apt-get update -qqy && apt-get install -qqy docker-ce

# Install Google Cloud SDK
# Code from here: https://github.com/GoogleCloudPlatform/cloud-sdk-docker/blob/master/Dockerfile
ENV CLOUD_SDK_VERSION 294.0.0
ENV PATH "$PATH:/opt/google-cloud-sdk/bin/"

RUN apt-get -qqy update && apt-get install -qqy \
	curl \
	gcc \
	python-dev \
	python-setuptools \
	apt-transport-https \
	lsb-release \
	openssh-client \
	git \
	gnupg && \
    pip3 install -U crcmod   && \
    export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)" && \
    echo "deb https://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" > /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - && \
    apt-get update && \
    apt-get install -y google-cloud-sdk=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-app-engine-python=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-app-engine-java=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-app-engine-go=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-datalab=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-datastore-emulator=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-pubsub-emulator=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-bigtable-emulator=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-cbt=${CLOUD_SDK_VERSION}-0 \
	kubectl && \
    gcloud config set core/disable_usage_reporting true && \
    gcloud config set component_manager/disable_update_check true && \
    gcloud config set metrics/environment github_docker_image && \
    gcloud --version && \
    docker --version && kubectl version --client

# Install terraform
ENV TERRAFORM_VERSION=0.12.25
ENV TERRAFORM_FILENAME=terraform_${TERRAFORM_VERSION}_linux_amd64.zip
ENV TERRAFORM_URL=https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/${TERRAFORM_FILENAME}
ENV TERRAFORM_SHA256SUM=e95daabd1985329f87e6d40ffe7b9b973ff0abc07a403f767e8658d64d733fb0

RUN wget -q ${TERRAFORM_URL} \
  && echo "${TERRAFORM_SHA256SUM}  ${TERRAFORM_FILENAME}" | sha256sum -c
RUN unzip ${TERRAFORM_FILENAME} -d /bin
RUN rm -f ${TERRAFORM_FILENAME}

# Install Cloud SQL Proxy
RUN wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy && \
  chmod +x cloud_sql_proxy && \
  mv cloud_sql_proxy /bin && \
  ln -s /bin/cloud_sql_proxy /bin/cloud-sql-proxy

# Install yarn
RUN curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get install -qqy nodejs yarn

# Other useful cli things
RUN apt-get install -qqy jq postgresql postgresql-contrib

# Install rust nightly
RUN curl https://sh.rustup.rs > rust-init
RUN sh rust-init -y --default-toolchain nightly-2019-02-05
ENV PATH="/root/.cargo/bin:$PATH"

# Install Golang
RUN add-apt-repository -y ppa:longsleep/golang-backports
RUN apt-get -qqy update
RUN apt-get install -qqy golang-go
