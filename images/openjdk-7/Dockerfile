FROM 762508870528.dkr.ecr.ap-southeast-2.amazonaws.com/future-airlines-jenkins-base-images/jenkins-slave-base

USER root
WORKDIR /opt

# Default to UTF-8 file.encoding
ENV LANG C.UTF-8

# Add Debian repository for openjdk-7
RUN echo "deb http://security.debian.org/debian-security jessie/updates main" >> /etc/apt/sources.list; \
  # Debian repository for jre-7 broken dependency "libgif4"
  echo "deb http://ftp.de.debian.org/debian jessie main" >> /etc/apt/sources.list;

RUN apt-get update && \
  apt-get install -y \
    tzdata-java/oldstable \
    openjdk-7-jre \
    ant

# Setup maven settings.xml
ADD configs/* ${MAVEN_HOME}/conf/

ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64

USER jenkins
WORKDIR /opt/build
