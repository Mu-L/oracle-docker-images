# Copyright (c) 2022, 2025 Oracle and/or its affiliates.
# Licensed under the Universal Permissive License v 1.0 as shown at https://oss.oracle.com/licenses/upl/
#
FROM ghcr.io/graalvm/jdk-community:21

LABEL org.opencontainers.image.source = "https://github.com/oracle/docker-images"

ARG KV_VERSION=25.1.13
ARG DOWNLOAD_ROOT="https://github.com/oracle/nosql/releases/download/v${KV_VERSION}/"
ARG DOWNLOAD_FILE="kv-ce-${KV_VERSION}.zip"
ARG DOWNLOAD_LINK="${DOWNLOAD_ROOT}/${DOWNLOAD_FILE}"

ENV KV_PROXY_PORT 8080
ENV KV_ADMIN_PORT 5999

ENV KV_PORT 5000
ENV KV_HARANGE 5010-5020
ENV KV_SERVICERANGE 5021-5049
ENV KV_ADDITIONAL_SAN=",DNS:proxy-nosql,DNS:kvlite-nosql-container-host"

# specific to secure configuration
RUN microdnf -y install openssl-3.0.1 && microdnf clean all

# hadolint ignore=DL4006
RUN useradd -d /kvroot  -m -s /bin/bash -u 1000 nosql-user && \
    mkdir -p /shared_conf && \
    chown -R nosql-user /shared_conf && \
    chown -R nosql-user /app/

USER nosql-user

WORKDIR "/app"

# hadolint ignore=DL4006
RUN curl -OLs $DOWNLOAD_LINK  && \
    jar tf $DOWNLOAD_FILE | grep "kv-$KV_VERSION/lib" > extract.libs && \
    jar xf $DOWNLOAD_FILE @extract.libs && \
    rm -f $DOWNLOAD_FILE extract.libs

WORKDIR "/app/kv-$KV_VERSION"

COPY --chown=nosql-user start-kvlite.sh .
RUN chmod +x start-kvlite.sh
COPY --chown=nosql-user sec/* sec/
RUN chmod +x sec/setup-http-proxy-sec.sh

VOLUME ["/kvroot"]

CMD ["bash", "-c", "./start-kvlite.sh"]
