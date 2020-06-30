FROM jenkins/jenkins:lts

# See README for updating this list
COPY plugins_extra.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt

# JCasC-plugin: file based Jenkins configuration
ENV CASC_JENKINS_CONFIG /var/jenkins_home/jcasc_configs
RUN mkdir /var/jenkins_home/jcasc_configs
# Files will be passed at container startup time in order to:
# - avoid rebuilding image any time the config changes
# - let the user change the image at runtime following these steps:
#       1. change any file inside jcasc_configs folder
#       2. visitnig JCasC plugin page
#       3. reload config from file 
# COPY jenkins.yaml /usr/share/jenkins/jcasc_configs

# Skip wizard
ENV JAVA_OPTS "-Djenkins.install.runSetupWizard=false ${JAVA_OPTS:-}"

