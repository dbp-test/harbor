ARG harbor_base_image_version
ARG harbor_base_namespace

FROM ${harbor_base_namespace}/harbor-registryctl-base:${harbor_base_image_version}


COPY ./make/photon/common/install_cert.sh /home/harbor
# COPY ./make/photon/registry/binary/registry /usr/bin/registry_DO_NOT_USE_GC
COPY ./make/photon/registryctl/start.sh /home/harbor
# COPY ../../config/registry/config.yml /home/harbor
# COPY ../../src/registryctl/main.go /home/harbor
# COPY ./make/photon/registryctl/harbor_registryctl /home/harbor

# RUN yum install wget -y \
# && yum update -y \
# && yum install wget -y \
# && wget https://go.dev/dl/go1.19.5.src.tar.gz \
# && rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.5.src.tar.gz \
# && export PATH=$PATH:/usr/local/go/bin
# # && wget https://dl.google.com/go/go1.13.5.linux-amd64.tar.gz && tar -C /usr/local/ -xzf go1.13.5.linux-amd64.tar.gz && export PATH=$PATH:/usr/local/go/bin

RUN chown -R harbor:harbor /etc/pki/tls/certs \
    # && chown harbor:harbor /home/harbor/harbor_registryctl && chmod u+x /home/harbor/harbor_registryctl \
    # && chown harbor:harbor /usr/bin/registry_DO_NOT_USE_GC && chmod u+x /usr/bin/registry_DO_NOT_USE_GC \
    && chown harbor:harbor /home/harbor/start.sh && chmod u+x /home/harbor/start.sh \
    && chown harbor:harbor /home/harbor/install_cert.sh && chmod u+x /home/harbor/install_cert.sh
    # && chown harbor:harbor /home/harbor/config.yml && chmod u+x /home/harbor/config.yml \
    # && chown harbor:harbor /home/harbor/main.go && chmod u+x /home/harbor/main.go


# HEALTHCHECK CMD curl --fail -s http://localhost:8081/api/health || curl -sk --fail --key /etc/harbor/ssl/registryctl.key --cert /etc/harbor/ssl/registryctl.crt https://localhost:8443/api/health || exit 1

COPY ../../../config/registry/config.yml /home/harbor
COPY ../../../src/registryctl/main.go /home/harbor

RUN  chown harbor:harbor /home/harbor/config.yml && chmod u+x /home/harbor/config.yml \
     && chown harbor:harbor /home/harbor/main.go && chmod u+x /home/harbor/main.go

FROM golang:1.16-alpine
WORKDIR /home/harbor
RUN  chmod +x /home/harbor
COPY ../../../config/registry/config.yml /home/harbor
COPY ../../../src/registryctl/main.go /home/harbor
RUN go env -w GO111MODULE=off
RUN go get -u .
RUN go run /home/harbor/main.go -c /home/harbor/config.yml

VOLUME ["/var/lib/registry"]

# ENTRYPOINT ["/home/harbor/start.sh"]

USER harbor
