FROM golang:alpine AS deps-go

ENV LC_COLLATE=C \
    LC_ALL=C.UTF-8 \
    LANG=C.UTF-8

RUN GO111MODULE="on" go get github.com/hashicorp/terraform-config-inspect
RUN GO111MODULE="on" go get github.com/terraform-linters/tflint
#RUN GO111MODULE="on" go get github.com/terraform-docs/terraform-docs@v0.14.1
RUN apk add --no-cache curl && curl -LJO https://github.com/terraform-docs/terraform-docs/releases/download/v0.14.1/terraform-docs-v0.14.1-linux-amd64.tar.gz && tar xvzf terraform-docs-*-linux-amd64.tar.gz terraform-docs -C /go/bin

FROM python:3-alpine as deps-python 
#RUN pip install --ignore-installed --target=/install j2cli[yaml] setuptools
RUN pip install --user --ignore-installed j2cli[yaml] setuptools

FROM node:erbium-alpine as deps-node

RUN apk add --no-cache \
    nodejs \
    npm
RUN npm install -g --force doctoc

FROM hashicorp/terraform:1.0.1
RUN mkdir /work
WORKDIR /work

RUN apk add --no-cache \
        curl \
        bash \
        grep \
        coreutils \
        jq \
        python3 \
        nodejs \
        make \
        bash-completion

COPY --from=deps-go /go/bin/ /usr/local/bin
COPY --from=deps-python /root/.local/lib /usr/lib
COPY --from=deps-python /root/.local/bin /usr/local/bin
RUN sed -i 's/local.*/bin\/python3/' /usr/local/bin/j2
COPY --from=deps-node /usr/local/lib/node_modules/doctoc /usr/lib/node_modules/doctoc
RUN ln -sf /usr/lib/node_modules/doctoc/doctoc.js /usr/local/bin/doctoc

COPY bashrc /root/.bashrc

ENTRYPOINT []
HEALTHCHECK NONE
CMD ["/bin/bash", "-l", "-i"]
