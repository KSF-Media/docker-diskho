FROM fpco/stack-build-small:lts-20.5

RUN rm -f /etc/apt/sources.list.d/cuda.list*
RUN ln -fs /usr/share/zoneinfo/Europe/Helsinki /etc/localtime

# Install gettext, contains the envsubst command, for env vars substitution
# Also install python3 and Jinja2, needed for Kubernetes templating
# As well as libyaml for pyaml and jinja2-cli for cli jinja tools
# along with some useful cli tools
RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends \
  gettext python3-pip libyaml-dev liblzma-dev software-properties-common sqitch \
  jq postgresql postgresql-contrib libpq-dev libssh2-1-dev libpcre++-dev libssl-dev && \
  apt-get -qqy clean && \
	rm -rf /var/lib/apt/lists/*
RUN pip3 install Jinja2
RUN pip3 install PyYAML
RUN pip3 install jinja2-cli

# Build Haskell dependencies that we typically use
RUN stack build --resolver lts-20.5 \
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
RUN stack install dhall-1.41.2 dhall-json-1.7.11 --resolver=lts-20.5

RUN wget -qO - https://packages.confluent.io/deb/6.2/archive.key | apt-key add -
RUN add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/6.2 stable main"

# Install docker
RUN wget -qO - https://download.docker.com/linux/ubuntu/gpg | apt-key add -
RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable"
RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends \
      curl \
      apt-transport-https \
      ca-certificates \
      software-properties-common \
      docker-ce && \
      apt-get -qqy clean && \
	    rm -rf /var/lib/apt/lists/*


# Install Google Cloud SDK
# Code from here: https://github.com/GoogleCloudPlatform/cloud-sdk-docker/blob/master/Dockerfile
ENV CLOUD_SDK_VERSION 430.0.0
ENV USE_GKE_GCLOUD_AUTH_PLUGIN True
ENV PATH "$PATH:/opt/google-cloud-sdk/bin/"

RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends \
	gcc \
	python3-dev \
	python3-setuptools \
	apt-transport-https \
	lsb-release \
	openssh-client \
	git \
	gnupg && \
    pip3 install -U crcmod   && \
    export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)" && \
    echo "deb https://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" > /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - && \
    apt-get -qqy update && \
    apt-get -qqy install --no-install-recommends google-cloud-sdk=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-app-engine-python=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-app-engine-java=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-app-engine-go=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-datastore-emulator=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-pubsub-emulator=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-bigtable-emulator=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-cbt=${CLOUD_SDK_VERSION}-0 \
	google-cloud-sdk-gke-gcloud-auth-plugin=${CLOUD_SDK_VERSION}-0 \
  kubectl && \
    gcloud config set core/disable_usage_reporting true && \
    gcloud config set component_manager/disable_update_check true && \
    gcloud config set metrics/environment github_docker_image && \
    gcloud --version && \
    docker --version && kubectl version --client && \
  apt-get -qqy clean && \
	rm -rf /var/lib/apt/lists/*


# Install terraform
RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends unzip && \
  apt-get -qqy clean && \
	rm -rf /var/lib/apt/lists/*
ENV TERRAFORM_VERSION=1.4.6
ENV TERRAFORM_FILENAME=terraform_${TERRAFORM_VERSION}_linux_amd64.zip
ENV TERRAFORM_URL=https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/${TERRAFORM_FILENAME}
ENV TERRAFORM_SHA256SUM=e079db1a8945e39b1f8ba4e513946b3ab9f32bd5a2bdf19b9b186d22c5a3d53b
RUN wget -q ${TERRAFORM_URL} \
  && echo "${TERRAFORM_SHA256SUM}  ${TERRAFORM_FILENAME}" | sha256sum -c
RUN /usr/bin/unzip ${TERRAFORM_FILENAME} -d /bin
RUN rm -f ${TERRAFORM_FILENAME}

# Install Cloud SQL Proxy
RUN wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy && \
  chmod +x cloud_sql_proxy && \
  mv cloud_sql_proxy /bin && \
  ln -s /bin/cloud_sql_proxy /bin/cloud-sql-proxy

# Install puppeteer dependencies
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -qqy --no-install-recommends google-chrome-stable fonts-freefont-ttf libxss1  \
    && rm -rf /var/lib/apt/lists/*

# Install yarn
RUN wget -qO - https://deb.nodesource.com/setup_16.x | bash -
RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends nodejs && \
  apt-get -qqy clean && \
	rm -rf /var/lib/apt/lists/*
RUN corepack enable
RUN npm install -g npm@8.10.0
RUN yarn set version 1.22.19

# Install dhall-to-(yaml|json) and puppeteer
RUN npm install --global --unsafe-perm=true dhall-to-yaml-cli@"1.7.6" dhall-to-json-cli@"1.7.6" puppeteer@"^14.1.1"

# Install Golang
RUN add-apt-repository -y ppa:longsleep/golang-backports
RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends golang-go && \
  apt-get -qqy clean && \
	rm -rf /var/lib/apt/lists/*

# Some additional dependencies we need that are missing from *-small images
RUN apt-get -qqy update && apt-get -qqy install --no-install-recommends pkg-config && \
  apt-get -qqy clean && \
	rm -rf /var/lib/apt/lists/*

# Install helm
RUN curl -fsSL -o helm.tgz https://get.helm.sh/helm-v3.11.0-linux-amd64.tar.gz && \
    tar xvf helm.tgz && mv linux-amd64/helm /usr/local/bin/helm && rm -rf linux-amd64 helm.tgz

# Symlink python to python3
RUN ln -s /usr/bin/python3 /usr/bin/python