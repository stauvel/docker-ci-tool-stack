FROM centos:7

ENV JBOSS_ZIP /root/jboss.zip
ENV JBOSS_HOME /opt/jboss-eap-7.0
ENV JBOSS_LOG /var/log/jboss
ENV WAR_PATH /root/v3_31-web.war

RUN INSTALL_PKGS="java-1.8.0-openjdk.x86_64" && \
    yum -y --setopt=tsflags=nodocs install $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all  && \
    localedef -f UTF-8 -i en_US en_US.UTF-8

# Install packages necessary
RUN yum -y install unzip && yum clean all

ADD $JBOSS_ZIP jboss.zip
RUN set -x \
	&& mkdir -p ${JBOSS_HOME} && unzip jboss.zip -d ${JBOSS_HOME}/

ADD $WAR_PATH $JBOSS_HOME/standalone/deployments/
VOLUME $JBOSS_LOG

CMD $JBOSS_HOME/bin/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0 --debug >> $JBOSS_LOG/jboss.log
