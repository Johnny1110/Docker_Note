FROM ubuntu:20.04
MAINTAINER Johnny Wang "jarvan1110@gmail.com"
ENV UPDATE_AT 2020-12-21
RUN apt-get update -yqq && apt-get -yqq install openjdk-8-jre
RUN mkdir -p /opt/webapp
WORKDIR /opt/webapp

EXPOSE 4000

CMD ["java", "-jar", "lib-demo-1.0-SNAPSHOT.jar"]
