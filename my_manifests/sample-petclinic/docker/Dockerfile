FROM ghcr.io/apache/skywalking-showcase/songs-service:3d6fc1c as build
ARG APP=skywalking-agent-petclinic-system


FROM antler/skywalking-spring-petclinic-rest-system:8.8.0
LABEL image=antler/skywalking-agent-petclinic-system:3d6fc1c
MAINTAINER Andrey Kozichev <akozichev@gmail.com>

RUN rm -rf /usr/local/petshop/skywalking-agent-petclinic-system/agent

COPY --from=build /skywalking/agent /usr/local/petshop/skywalking-agent-petclinic-system/
