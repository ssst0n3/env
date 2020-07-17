# set by docker-compose or manually
# ARG VERSION=2.1.1
ARG VERSION
ARG RELEASE=${VERSION}.RELEASE
ARG TAG=v${RELEASE}
ARG FILE_NAME=${TAG}.tar.gz
ARG DIR_NAME=spring-cloud-config-${RELEASE}

FROM busybox
ARG VERSION
ARG TAG
ENV https_proxy=http://100.66.25.159:8118
RUN wget -O spring-cloud-config.tar.gz https://github.com/spring-cloud/spring-cloud-config/archive/${TAG}.tar.gz
RUN wget -O settings.xml https://mirrors.huaweicloud.com/v1/configurations/maven

FROM maven
ARG VERSION
ARG DIR_NAME
ARG RELEASE
COPY --from=0 spring-cloud-config.tar.gz /
COPY --from=0 settings.xml /root/.m2/
RUN tar xzvf spring-cloud-config.tar.gz
WORKDIR /${DIR_NAME}/spring-cloud-config-server
RUN mvn package spring-boot:repackage -P spring -Dmaven-checkstyle-plugin.includeTestSourceDirectory=false -Dmaven-checkstyle-plugin.failsOnError=false -Dmaven.test.skip=true -Dmaven.javadoc.skip=true -Dmaven.source.skip=true

FROM openjdk:8u252-jre
ARG DIR_NAME
ARG RELEASE
RUN useradd -m app
USER app
WORKDIR /home/app/

COPY --from=1 /${DIR_NAME}/spring-cloud-config-server/target/spring-cloud-config-server-${RELEASE}.jar /home/app/spring-cloud-config-server.jar
ENTRYPOINT ["java", "-jar", "spring-cloud-config-server.jar"]