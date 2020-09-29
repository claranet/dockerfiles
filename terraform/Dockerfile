FROM golang:alpine AS builder

WORKDIR /home

ENV LC_COLLATE=C \
    LC_ALL=C.UTF-8 \
    LANG=C.UTF-8

RUN apk add --no-cache \
        curl \
        gzip

RUN GO111MODULE=on go get github.com/hashicorp/terraform-config-inspect && mv /go/bin/* /usr/local/bin/

RUN curl -Lo /usr/local/bin/terraform-docs "https://github.com/$(curl -Ls https://github.com/segmentio/terraform-docs/releases | grep terraform-docs-v0.9.1-linux-amd | head -n 1 | cut -d '"' -f 2)"

RUN curl -sL "https://github.com$(curl -s https://github.com/terraform-linters/tflint/releases | grep -m 1 linux_amd64 | cut -d '"' -f 2)" | zcat | tee /usr/local/bin/tflint > /dev/null

RUN chmod +x /usr/local/bin/*

FROM hashicorp/terraform:0.13.3

RUN apk add --no-cache \
        curl \
        bash \
        grep \
        coreutils \
        jq

COPY --from=builder /usr/local/bin/ /usr/local/bin

ENTRYPOINT ["/bin/bash"]
HEALTHCHECK NONE
CMD []
