FROM jenkins/jenkins:lts

USER root
RUN apt-get update \
      && apt-get install -y sudo curl\
      && apt-get install -y libltdl7\
      && rm -rf /var/lib/apt/lists/*
RUN echo "jenkins ALL=NOPASSWD: ALL" >> /etc/sudoers

RUN echo "Chrome Installation begins..."
#Fix https://github.com/jenkinsci/docker/issues/543
COPY 99fixbadproxy /etc/apt/apt.conf.d/99fixbadproxy
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
RUN sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
RUN apt-get update
RUN apt-get install -y google-chrome-stable
ENV CHROME_BIN /usr/bin/google-chrome
RUN echo "Chrome installation complete..."
RUN $CHROME_BIN --version

# getting the docker-cli
# --- Attention: docker.sock needs to be mounted as volume in docker-compose.yml
# see: https://issues.jenkins-ci.org/browse/JENKINS-35025
# see: https://get.docker.com/builds/
# see: https://wiki.jenkins-ci.org/display/JENKINS/CloudBees+Docker+Custom+Build+Environment+Plugin#CloudBeesDockerCustomBuildEnvironmentPlugin-DockerinDocker
RUN curl -sSL -o /bin/docker https://get.docker.io/builds/Linux/x86_64/docker-latest 
RUN chmod +x /bin/docker

USER jenkins

# installing specific list of plugins. see: https://github.com/jenkinsci/docker#preinstalling-plugins
COPY plugins.txt /usr/share/jenkins/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/plugins.txt

# Adding default Jenkins Jobs
COPY jobs/1-test-job.xml /usr/share/jenkins/ref/jobs/1-test-job/config.xml
COPY jobs/2-pwc-ui-shared-job.xml /usr/share/jenkins/ref/jobs/2-pwc-ui-shared-job/config.xml
COPY jobs/3-pwc-ui-socle-job.xml /usr/share/jenkins/ref/jobs/3-pwc-ui-socle-job/config.xml

# Expose Continuous Integration Data :
VOLUME /var/cip
VOLUME /var/lib/jenkins

############################################
# Configure Jenkins
############################################
# Jenkins settings
COPY config/config.xml /usr/share/jenkins/ref/config.xml

# Jenkins Settings, i.e. Maven, Groovy, ...
COPY config/hudson.tasks.Maven.xml /usr/share/jenkins/ref/hudson.tasks.Maven.xml
COPY config/hudson.plugins.groovy.Groovy.xml /usr/share/jenkins/ref/hudson.plugins.groovy.Groovy.xml
COPY config/jenkins.plugins.nodejs.tools.NodeJSInstallation.xml /usr/share/jenkins/ref/jenkins.plugins.nodejs.tools.NodeJSInstallation.xml
COPY config/maven-global-settings-files.xml /usr/share/jenkins/ref/maven-global-settings-files.xml
COPY config/org.jenkinsci.plugins.configfiles.GlobalConfigFiles.xml /usr/share/jenkins/ref/org.jenkinsci.plugins.configfiles.GlobalConfigFiles.xml

# SSH Keys & Credentials
COPY config/credentials.xml /usr/share/jenkins/ref/credentials.xml
COPY config/ssh-keys/cd-demo /usr/share/jenkins/ref/.ssh/id_rsa
COPY config/ssh-keys/cd-demo.pub /usr/share/jenkins/ref/.ssh/id_rsa.pub

# tell Jenkins that no banner prompt for pipeline plugins is needed
# see: https://github.com/jenkinsci/docker#preinstalling-plugins
RUN echo 2.0 > /usr/share/jenkins/ref/jenkins.install.UpgradeWizard.state

