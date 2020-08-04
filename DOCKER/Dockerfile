FROM openjdk:8
RUN apt update -y && apt install net-tools -y
RUN apt install curl -y
COPY spring-boot-with-prometheus-0.1.0.jar /spring-boot-with-prometheus-0.1.0.jar
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT [ "/entrypoint.sh" ]
#HEALTHCHECK CMD curl --fail http://localhost:8080/ || exit 1