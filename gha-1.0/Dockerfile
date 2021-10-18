FROM ubuntu:20.04

# Install base packages
RUN apt-get update
RUN apt-get install -qqy curl ruby-full sudo make git

# Install yarn
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -qqy nodejs
RUN npm install --global yarn@"^1.22.10" dhall-to-yaml-cli@"1.7.6" dhall-to-json-cli@"1.7.6"
