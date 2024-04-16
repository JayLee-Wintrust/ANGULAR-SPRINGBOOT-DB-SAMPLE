FROM docker.io/maven:3.6.3-amazoncorretto-8 as maven-build-container

WORKDIR /usr/src/app
COPY pom.xml pom.xml
COPY src src
# Ignoring certifiacte issue in WINTRUST laptop. Not be used in PROD.
RUN mvn clean package -Dmaven.wagon.http.ssl.insecure=true


FROM docker.io/tomcat:9-jdk8 as tomcat-container

# Application Details
ARG POM_XML_ARTIFACT_ID=employeemanager
ARG POM_XML_ARTIFACT_VERSION=0.0.1-SNAPSHOT
ARG TOMCAT_CONF_DIR=tomcat-conf

ENV CATALINA_OPTS="-Xms1024m -Xmx4096m -XX:MetaspaceSize=512m -XX:MaxMetaspaceSize=512m -Xss512k"
RUN chown -R 1001:1001 ${CATALINA_HOME}
COPY --from=maven-build-container --chown=1001:1001 /usr/src/app/target/${POM_XML_ARTIFACT_ID}-${POM_XML_ARTIFACT_VERSION}.war ${CATALINA_HOME}/webapps/ROOT.war
# ADD source dest ${TOMCAT_CONF_DIR}/* ${CATALINA_HOME}/conf/

USER 1001