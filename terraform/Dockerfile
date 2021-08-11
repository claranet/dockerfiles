FROM golang:alpine AS deps-go

ENV LC_COLLATE=C \
    LC_ALL=C.UTF-8 \
    LANG=C.UTF-8

RUN GO111MODULE="on" go get github.com/hashicorp/terraform-config-inspect
RUN GO111MODULE="on" go get github.com/terraform-linters/tflint
#RUN GO111MODULE="on" go get github.com/terraform-docs/terraform-docs@v0.14.1
RUN apk add --no-cache curl && curl -LJO https://github.com/terraform-docs/terraform-docs/releases/download/v0.14.1/terraform-docs-v0.14.1-linux-amd64.tar.gz && tar xvzf terraform-docs-*-linux-amd64.tar.gz terraform-docs -C /go/bin

FROM alpine as deps-python 
RUN apk add --update --no-cache python3 py3-pip
RUN apk add --update --no-cache libxslt-dev libxml2-dev libxml2 libxslt libc-dev gcc python3-dev
RUN pip install --prefix /usr/local --ignore-installed setuptools j2cli[yaml] pyspelling

FROM node:erbium-alpine as deps-node

RUN apk add --no-cache \
    nodejs \
    npm
RUN npm install -g --force doctoc markdown-link-check

FROM hashicorp/terraform:1.0.4
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
        bash-completion \
        aspell \
        aspell-en

COPY --from=deps-python /usr/local /usr/local
RUN ln -sf python3 /usr/bin/python
COPY --from=deps-go /go/bin/ /usr/local/bin
COPY --from=deps-node /usr/local/lib/node_modules/doctoc /usr/lib/node_modules/doctoc
RUN ln -sf /usr/lib/node_modules/doctoc/doctoc.js /usr/local/bin/doctoc
COPY --from=deps-node /usr/local/lib/node_modules/markdown-link-check /usr/lib/node_modules/markdown-link-check
RUN ln -sf /usr/lib/node_modules/markdown-link-check/markdown-link-check /usr/local/bin/markdown-link-check

ENV PATH=/root/.local/bin:${PATH} \
    SHELL=/bin/bash \
    PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.9/site-packages

COPY bashrc /root/.bashrc

ENTRYPOINT []
HEALTHCHECK NONE
CMD ["/bin/bash", "-l", "-i"]
