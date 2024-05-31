# Gebruik de nieuwste versie van Ubuntu als basis
FROM ubuntu:22.04

# Maintainers
MAINTAINER Ivan Ermilov <ivan.s.ermilov@gmail.com>
MAINTAINER Giannis Mouchakis <gmouchakis@iit.demokritos.gr>

# Installeer vereiste pakketten
RUN apt-get update && apt-get install -y \
    openjdk-8-jdk \
    net-tools \
    curl \
    netcat-openbsd \
    gnupg \
    libsnappy-dev \
    python3-pip \
    python3-venv \
    && rm -rf /var/lib/apt/lists/*

# Stel de JAVA_HOME omgeving variabele in
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-arm64/

# Download en installeer Hadoop
RUN curl -O https://dist.apache.org/repos/dist/release/hadoop/common/KEYS \
    && gpg --import KEYS

ENV HADOOP_VERSION 3.3.6
ENV HADOOP_URL https://dist.apache.org/repos/dist/release/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz

RUN set -x \
    && curl -fSL "$HADOOP_URL" -o /tmp/hadoop.tar.gz \
    && curl -fSL "$HADOOP_URL.asc" -o /tmp/hadoop.tar.gz.asc \
    && gpg --verify /tmp/hadoop.tar.gz.asc \
    && tar -xvf /tmp/hadoop.tar.gz -C /opt/ \
    && rm /tmp/hadoop.tar.gz*

# Maak symbolische links en directories aan
RUN ln -s /opt/hadoop-$HADOOP_VERSION/etc/hadoop /etc/hadoop \
    && mkdir /opt/hadoop-$HADOOP_VERSION/logs \
    && mkdir /hadoop-data

# Stel Hadoop omgeving variabelen in
ENV HADOOP_HOME=/opt/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=/etc/hadoop
ENV MULTIHOMED_NETWORK=1
ENV USER=root
ENV PATH $HADOOP_HOME/bin/:$PATH

# Maak een Python virtuele omgeving aan en installeer vereiste Python pakketten
RUN python3 -m venv /opt/venv \
    && /opt/venv/bin/pip install --upgrade pip \
    && /opt/venv/bin/pip install pyspark pyarrow setuptools mrjob numpy

# Zet de virtuele omgeving in de PATH variabele
ENV PATH="/opt/venv/bin:$PATH"

# Voeg het entrypoint script toe
ADD entrypoint.sh /entrypoint.sh
RUN chmod a+x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
