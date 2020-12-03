FROM maven:3.6.3-jdk-8 AS maven
WORKDIR /app
COPY pom.xml pom.xml
COPY src src
RUN mvn clean install

FROM tomcat:8.0.51-jre8-alpine
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=maven /app/target/*.war /usr/local/tomcat/webapps/ROOT.war
CMD ["catalina.sh","run"]
