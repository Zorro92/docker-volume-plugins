ARG UBUNTU_VERSION
FROM --platform=${TARGETPLATFORM:-linux/amd64} ubuntu:${UBUNTU_VERSION:-25.10} as base

ENV DEBIAN_FRONTEND="noninteractive"

RUN apt update && \
    apt install -y glusterfs-client curl rsyslog tini && \
    apt clean && rm -rf /var/lib/apt/lists/* && \
    rm -rf "/tmp/*" "/root/.cache" /var/log/lastlog && \
    mkdir -p /var/lib/glusterd /etc/glusterfs && \
    touch /etc/glusterfs/logger.conf

COPY glusterfs-volume-plugin/rsyslog.conf /etc/rsyslog.conf

ARG GO_VERSION
FROM --platform=${TARGETPLATFORM:-linux/amd64} golang:${GO_VERSION:-1.23}-alpine as dev

RUN apk update && apk add git

COPY glusterfs-volume-plugin/ /go/src/github.com/marcelo-ochoa/docker-volume-plugins/glusterfs-volume-plugin
COPY mounted-volume/ /go/src/github.com/marcelo-ochoa/docker-volume-plugins/mounted-volume

RUN cd /go/src/github.com/marcelo-ochoa/docker-volume-plugins/glusterfs-volume-plugin && \
    CGO_ENABLED=0 GOOS=linux go build -o /glusterfs-volume-plugin

FROM base

COPY --from=dev /glusterfs-volume-plugin /
