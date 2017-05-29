# Pull base image.
FROM ubuntu:16.04

RUN apt-get update && apt-get upgrade -y
RUN apt-get install openjdk-8* -y
RUN apt-get install maven -y
RUN mkdir -p /my_custom_app/upload/

COPY Code/ /my_custom_app/

WORKDIR /my_custom_app/
ENV MAVEN_HOME /usr/bin/mvn
ENV JAVA_OPTS="-Dupload-dir=/my_custom_app/upload"
ENTRYPOINT exec mvn $JAVA_OPTS spring-boot:run
