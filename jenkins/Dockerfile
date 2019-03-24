FROM jenkins/jenkins:lts

# USER jenkins
USER root
RUN apt-get update -qq \
    && apt-get install -qqy apt-transport-https ca-certificates curl gnupg2 software-properties-common 
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
RUN apt-get update -qq \
    && apt-get install docker-ce -y


ARG httpProxy
ARG noProxy

ENV HTTPS_PROXY=${httpProxy} \
    https_proxy=${httpProxy} \
    HTTP_PROXY=${httpProxy} \
    http_proxy=${httpProxy} \
    ftp_proxy=${httpProxy} \
    FTP_PROXY=${httpProxy} \
    NO_PROXY=${noProxy} \
    no_proxy=${noProxy}

# ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=false"
 
# RUN sudo usermod -aG docker jenkins
# USER jenkins


COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt

# Configure Git
RUN \
  git config --global push.default simple && \
  git config --global user.name "Jenkins" && \
  git config --global user.email jenkins@example.com

# Copy files needed in the container
COPY init.groovy.d/* /usr/share/jenkins/ref/init.groovy.d/
COPY seed-job/ /usr/share/jenkins/ref/jobs/seed-job
COPY scriptApproval.xml.override /usr/share/jenkins/ref/scriptApproval.xml.override
COPY compute-seed-job-hash.sh /tmp/

# Pre-approve the seed job
RUN /tmp/compute-seed-job-hash.sh /usr/share/jenkins/ref/jobs/seed-job/workspace/job.groovy.override /usr/share/jenkins/ref/scriptApproval.xml.override

# curl -s -k "http://admin:admin@localhost:8080/pluginManager/api/json?depth=1" | jq -r '.plugins[].shortName' | tee plugins.txt