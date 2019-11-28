FROM 762508870528.dkr.ecr.ap-southeast-2.amazonaws.com/future-airlines-jenkins-base-images:python-36

ENV PYTHON_VERSION 3.6.5
ENV SPARK_VERSION 2.4.3
ENV HADOOP_VERSION 3.2.0
ENV PY4J_VERSION 0.10.6

USER root
WORKDIR /opt

# HADOOP
ENV HADOOP_HOME /usr/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV PATH $PATH:$HADOOP_HOME/bin
RUN curl \
    "http://archive.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz" \
    | gunzip \
    | tar -x -C /usr/ && \
    rm -rf $HADOOP_HOME/share/doc

# SPARK

ENV SPARK_PACKAGE spark-$SPARK_VERSION-bin-without-hadoop
ENV SPARK_HOME /usr/spark-$SPARK_VERSION
ENV SPARK_DIST_CLASSPATH="$HADOOP_HOME/etc/hadoop/*:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/yarn/lib/*:$HADOOP_HOME/share/hadoop/yarn/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*"
ENV PATH $PATH:$SPARK_HOME/bin
RUN curl \
    "http://archive.apache.org/dist/spark/spark-$SPARK_VERSION/$SPARK_PACKAGE.tgz" \
    | gunzip \
    | tar x -C /usr/ && \
    mv /usr/$SPARK_PACKAGE $SPARK_HOME && \
    rm -rf $SPARK_HOME/examples $SPARK_HOME/ec2

RUN spark-shell --packages org.apache.hadoop:hadoop-aws:$HADOOP_VERSION

# Copy / cache the installed to be added on the classpath
RUN mkdir -p /opt/.ivy2/jars/ && \
	cp -rf /root/.ivy2/jars/ /opt/.ivy2 && \
    rm -rf /root/.ivy2/jars

WORKDIR /opt/build
USER jenkins
