FROM openjdk:8-alpine
RUN apk add ttf-dejavu 
RUN mkdir -p /opt/jenkins
ADD jenkins.war /opt/jenkins/
WORKDIR /opt/jenkins
CMD [ "java","-jar","-Djava.awt.headless=true","/opt/jenkins/jenkins.war" ] 