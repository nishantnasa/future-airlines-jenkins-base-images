FROM jenkins/jnlp-slave:latest

USER root

ARG credstash_go_version=0.3.5
ARG maven_version=3.3.9

WORKDIR /opt

# Configure sudo
RUN apt-get update \
      && apt-get install -y sudo \
      # Add sudo log file
      && printf "\nDefaults    logfile=/var/log/sudolog\n" >> /etc/sudoers \
      # Delete sudo user-group privileges
      && sed -i '/^%sudo/d' /etc/sudoers
COPY config/sudoers.d/* /etc/sudoers.d/

# Download packages

# Credstash
ADD https://github.com/winebarrel/gcredstash/releases/download/v${credstash_go_version}/gcredstash-v${credstash_go_version}-linux-amd64.gz .
# Maven
ADD http://apache.osuosl.org/maven/maven-3/${maven_version}/binaries/apache-maven-${maven_version}-bin.tar.gz .

# Install packages

RUN tar -zxf apache-maven-${maven_version}-bin.tar.gz \
      && ln -s /opt/apache-maven-${maven_version} /opt/apache-maven \
      && ln -s /opt/apache-maven/bin/mvn /usr/bin/mvn \
      && gunzip -c gcredstash-v${credstash_go_version}-linux-amd64.gz > /usr/bin/gcredstash \
      && rm -f gcredstash-v${credstash_go_version}-linux-amd64.gz \
      && chmod 755 /usr/bin/gcredstash \
       && apt-get update \
      && apt-get install -y jq docker \
      && mkdir /opt/build

ENV MAVEN_HOME /opt/apache-maven

RUN mkdir -p /opt/build \
      && chown -R jenkins:jenkins /opt/build
WORKDIR /opt/build

USER jenkins
