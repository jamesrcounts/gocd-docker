# Build stable release using: docker build --build-args GO_VERSION=16.0-xxxx -t gocd-agent .
# Build experimental build using: docker build --build-args GO_VERSION=16.0-xxxx  --build-args DOWNLOAD_URL=https://download.go.cd/experimental/binaries -t gocd-agent .
FROM phusion/baseimage:0.9.18
MAINTAINER GoCD <go-cd-dev@googlegroups.com>

ARG GO_VERSION="16.8.0-3929"
ARG DOWNLOAD_URL="https://download.go.cd/binaries"

RUN rm -rf /etc/service/sshd /etc/my_init.d/00_regen_ssh_host_keys.sh
RUN curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -

# Docker cli pre-reqs
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
RUN echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" >> /etc/apt/sources.list.d/docker.list

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y -q \
      apt-transport-https \
      build-essential \
      ca-certificates \
      docker-engine \
      unzip \
      openjdk-7-jre-headless \
      git \
      mercurial \
      nodejs \
      subversion

RUN npm install -g grunt-cli

WORKDIR /tmp
RUN curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip" && \
  unzip awscli-bundle.zip && \
  ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws

RUN mkdir /etc/service/go-agent
ADD go-agent-start.sh /etc/service/go-agent/run

ADD $DOWNLOAD_URL/$GO_VERSION/deb/go-agent_${GO_VERSION}_all.deb /tmp/go-agent.deb

WORKDIR /tmp
RUN dpkg -i /tmp/go-agent.deb
RUN sed -i -e 's/DAEMON=Y/DAEMON=N/' /etc/default/go-agent

# Grant access to docker.sock
RUN usermod -aG staff go

RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

CMD ["/sbin/my_init"]
