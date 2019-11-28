FROM 762508870528.dkr.ecr.ap-southeast-2.amazonaws.com/future-airlines-jenkins-base-images/jenkins-slave-base

USER root
WORKDIR /opt

ARG jmeter_version=5.1.1
ARG jmeter_home=/opt/apache-jmeter
ARG jmeter_plugins_manager_version=1.3
ARG cmdrunner_version=2.2
ARG json_lib_version=2.4
ARG json_lib_full_version=${json_lib_version}-jdk15

# Install additional tools
RUN apt-get update \
      && apt-get install -y ant

# Setup maven settings.xml
ADD configs/* ${MAVEN_HOME}/conf/

# Jmeter installation
RUN wget http://mirror.intergrid.com.au/apache//jmeter/binaries/apache-jmeter-${jmeter_version}.zip \
      && unzip apache-jmeter-${jmeter_version}.zip \
      && ln -s /opt/apache-jmeter-${jmeter_version} /opt/apache-jmeter


# Jmeter Plugin installation
RUN curl --location --silent --show-error --output ${jmeter_home}/lib/ext/jmeter-plugins-manager-${jmeter_plugins_manager_version}.jar http://search.maven.org/remotecontent?filepath=kg/apc/jmeter-plugins-manager/${jmeter_plugins_manager_version}/jmeter-plugins-manager-${jmeter_plugins_manager_version}.jar \
&& curl --location --silent --show-error --output ${jmeter_home}/lib/cmdrunner-${cmdrunner_version}.jar http://search.maven.org/remotecontent?filepath=kg/apc/cmdrunner/${cmdrunner_version}/cmdrunner-${cmdrunner_version}.jar \
&& curl --location --silent --show-error --output ${jmeter_home}/lib/json-lib-${json_lib_full_version}.jar https://search.maven.org/remotecontent?filepath=net/sf/json-lib/json-lib/${json_lib_version}/json-lib-${json_lib_full_version}.jar \
&& java -cp ${jmeter_home}/lib/ext/jmeter-plugins-manager-${jmeter_plugins_manager_version}.jar org.jmeterplugins.repository.PluginManagerCMDInstaller \
&& ${jmeter_home}/bin/PluginsManagerCMD.sh install-all-except jpgc-autostop \
&& ${jmeter_home}/bin/PluginsManagerCMD.sh install jpgc-casutg

ENV PATH="${PATH}:/opt/apache-jmeter/bin"

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64

USER jenkins
WORKDIR /opt/build
