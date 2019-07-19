FROM openjdk:8-alpine
RUN apk add ttf-dejavu git
WORKDIR /opt/jenkins
RUN mkdir -p /opt/jenkins/home
ADD jenkins.war /opt/jenkins/
ADD sonar-runner-dist-2.4.zip /opt/jenkins
ADD apache-maven-3.6.1-bin.zip /opt/jenkins
RUN unzip sonar-runner-dist-2.4.zip && unzip apache-maven-3.6.1-bin.zip
CMD [ "java","-jar","-Djava.awt.headless=true","/opt/jenkins/jenkins.war" ] 