FROM ubuntu:20.04

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8 
ENV LC_ALL=en_US.UTF-8 

# install packages
RUN apt-get update && apt-get upgrade -y
RUN DEBIAN_FRONTEND="noninteractive" apt-get install -y \
      sudo \
      gawk \
      wget \
      curl \
      git \
      vim \
      tar \
      ssh \
      ftp \
      locales \
      openjdk-17-jdk

# update locale
RUN sed -i -e "s/# $LANG.*/$LANG UTF-8/" /etc/locale.gen
RUN locale-gen
RUN update-locale LANG=$LANG LANGUAGE=$LANGUAGE LC_ALL=$LC_ALL

# generate RSA1 RSA DSA ECDSA keys
RUN ssh-keygen -A

# permit root ssh login
RUN sed -i "s+#PermitRootLogin prohibit-password+PermitRootLogin prohibit-password+g" /etc/ssh/sshd_config
COPY --chown=root:root id_rsa.pub /root/.ssh/authorized_keys

# instal Maven 3.8.4
WORKDIR /root
RUN wget https://dlcdn.apache.org/maven/maven-3/3.8.5/binaries/apache-maven-3.8.5-bin.tar.gz
RUN echo "89ab8ece99292476447ef6a6800d9842bbb60787b9b8a45c103aa61d2f205a971d8c3ddfb8b03e514455b4173602bd015e82958c0b3ddc1728a57126f773c743 apache-maven-3.8.5-bin.tar.gz" >> apache-maven-3.8.5-bin.tar.gz.sha512
RUN sha512sum -c apache-maven-3.8.5-bin.tar.gz.sha512
RUN mkdir -p /usr/local/apache-maven
RUN tar -xzvf apache-maven-3.8.5-bin.tar.gz -C /usr/local/apache-maven
RUN echo 'export M2_HOME=/usr/local/apache-maven/apache-maven-3.8.5' >> ~/.bashrc
RUN echo 'export M2=$M2_HOME/bin' >> ~/.bashrc
RUN echo 'export MAVEN_OPTS="-Xms256m -Xmx512m"' >> ~/.bashrc
RUN echo 'export PATH=$M2:$PATH' >> ~/.bashrc

# setup server source files
RUN git clone https://github.com/signalapp/Signal-Server.git ~/Signal-Server
RUN ln -s ~/Signal-Server/service/config/sample.yml ~/Signal-Server/service/config/config.yml

ENTRYPOINT service ssh start > /dev/null 2>&1; \
           echo "mvn clean install -DskipTests"; \
           /bin/bash