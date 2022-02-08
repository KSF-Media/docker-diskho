FROM ubuntu:20.04

# Install base packages
RUN apt-get update \
    && apt-get install -qqy wget gnupg \
    && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -qqy --no-install-recommends curl ruby-full sudo make git google-chrome-stable fonts-freefont-ttf libxss1  \
    && rm -rf /var/lib/apt/lists/*

# Install yarn
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -qqy nodejs
RUN npm install --global --unsafe-perm=true yarn@"^1.22.10" dhall-to-yaml-cli@"1.7.6" dhall-to-json-cli@"1.7.6" puppeteer@"^13.1.3"
