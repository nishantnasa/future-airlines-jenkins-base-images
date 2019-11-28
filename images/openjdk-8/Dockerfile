FROM 762508870528.dkr.ecr.ap-southeast-2.amazonaws.com/future-airlines-jenkins-base-images:jenkins-slave-base

USER root
WORKDIR /opt

ARG cmdrunner_version=2.2
ARG json_lib_version=2.4
ARG json_lib_full_version=${json_lib_version}-jdk15

# Install additional tools
RUN apt-get update \
      && apt-get install -y ant

# Setup maven settings.xml
ADD configs/* ${MAVEN_HOME}/conf/

ENV PATH="${PATH}:/opt/apache-jmeter/bin"

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64

USER jenkins
WORKDIR /opt/build
