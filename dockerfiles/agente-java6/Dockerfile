
FROM ubuntu:12.04
MAINTAINER Emilio Montes <3milio.mc@gmail.com>


#################################################
# Inspired by
# https://github.com/SeleniumHQ/docker-selenium/blob/master/Base/Dockerfile
# https://github.com/cloudbees/java-build-tools-dockerfile/blob/master/Dockerfile
#################################################

#================================================
# Set Environment Variables
#================================================
ENV JENKINS_HOME /var/lib/jenkins
ENV JAVA_VERSION 6
ENV JENKINS_REMOTE_VERSION 2.9
ENV SELENIUM_VERSION 2.48
ENV SELENUIM_SUB_VERSION 2
ENV MAVEN_VERSION 3.0.4
ENV MAVEN_HOME /usr/share/maven
ENV SWARM_CLIENT_VERSION 2.2
ENV DOCKER_COMPOSE_VERSION 1.8.0
ENV COMMAND_OPTIONS ""

#================================================
# Customize sources for apt-get
#================================================
RUN  echo "deb http://archive.ubuntu.com/ubuntu vivid main universe\n" > /etc/apt/sources.list \
  && echo "deb http://archive.ubuntu.com/ubuntu vivid-updates main universe\n" >> /etc/apt/sources.list

RUN echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections
RUN echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections



#RUN apt-get update && apt-get install -y apt-transport-https ca-certificates

RUN apt-get update -qqy &&\
    apt-get install -qqy --no-install-recommends software-properties-common && \
    add-apt-repository ppa:webupd8team/java && \
    apt-add-repository ppa:brightbox/ruby-ng && \
    apt-get update && \
    apt-get install -y --no-install-recommends oracle-java${JAVA_VERSION}-installer

RUN apt-get update && apt-get install -y wget curl nano less tree ruby2.3 ruby2.3-dev tar zip unzip
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
RUN echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-engine

RUN apt-get update && apt-get install -y python python-pip git groff


#==========
# Maven
#==========

RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
  && mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

#==========
# Selenium
#==========
#RUN  mkdir -p /opt/selenium \
#  && wget --no-verbose http://selenium-release.storage.googleapis.com/${SELENIUM_VERSION}/selenium-server-standalone-${SELENIUM_VERSION}.${SELENUIM_SUB_VERSION}.jar -O /opt/selenium/selenium-server-standalone.jar

#===========
# Cucumber
#===========
#RUN gem install cucumber && \
#    gem install gherkin && \
#    gem install capybara && \
#    gem install rspec && \
#    gem install selenium-webdriver

#========================================
# Add normal user with passwordless sudo
#========================================
RUN useradd jenkins --shell /bin/bash -d ${JENKINS_HOME} --create-home \
  && usermod -a -G sudo jenkins \
  && echo 'ALL ALL = (ALL) NOPASSWD: ALL' >> /etc/sudoers \
  && echo 'jenkins:jenkins' | chpasswd

#RUN adduser -G root -D jenkins

#===============
# XVFB & FIREFOX
#===============
#RUN apt-get update -qqy \
#  && apt-get -qqy --no-install-recommends install \
#    xvfb firefox \
#  && rm -rf /var/lib/apt/lists/*

#========================
# Selenium Configuration
#========================
#COPY config.json /opt/selenium/config.json
#ENV SCREEN_WIDTH 1360
#ENV SCREEN_HEIGHT 1020
#ENV SCREEN_DEPTH 24
#ENV DISPLAY :99.0

#====================================
# Scripts to run Selenium Standalone
#====================================
#COPY entry_point.sh /opt/bin/entry_point.sh
#RUN chmod +x /opt/bin/entry_point.sh

#====================================
# MYSQL CLIENT
#====================================
#RUN apt-get update -qqy \
#  && apt-get -qqy --no-install-recommends install \
#    mysql-client \
#  && rm -rf /var/lib/apt/lists/*

#====================================
# Setup JNLP Slave for Jenkins
#====================================

#RUN curl --create-dirs -sSLo /usr/share/jenkins/slave.jar http://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${JENKINS_REMOTE_VERSION}/remoting-${JENKINS_REMOTE_VERSION}.jar \
#  && chmod 755 /usr/share/jenkins \
#  && chmod 644 /usr/share/jenkins/slave.jar

#COPY jenkins-slave /usr/local/bin/jenkins-slave

#RUN chmod a+rwx ${JENKINS_HOME}
#WORKDIR ${JENKINS_HOME}
#USER jenkins

#ENTRYPOINT ["/opt/bin/entry_point.sh", "/usr/local/bin/jenkins-slave"]

#EXPOSE 4444


#===================================
# Forma 2
#===================================

RUN wget -q https://repo.jenkins-ci.org/releases/org/jenkins-ci/plugins/swarm-client/${SWARM_CLIENT_VERSION}/swarm-client-${SWARM_CLIENT_VERSION}-jar-with-dependencies.jar -P /home/jenkins/ 
RUN pip install docker-compose 

#ENTRYPOINT ["/opt/bin/entry_point.sh", "/usr/local/bin/jenkins-slave"]


COPY run.sh /run.sh
RUN chmod +x /run.sh

CMD ["/run.sh"]

