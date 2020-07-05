FROM golang:buster AS installer

ENV S3_SYNC_VERSION=v1.0.0-beta2
ENV CF_PURGE_VERSION=v1.0.0-beta1
ENV HUGO_VERSION=v0.73.0

# Build s3-sync
WORKDIR /go/src/github.com/tonymackay/s3-sync
RUN git clone https://github.com/tonymackay/s3-sync . && git checkout ${S3_SYNC_VERSION}
RUN go install -ldflags=-X=main.version=${S3_SYNC_VERSION}

# Build cf-purge
WORKDIR /go/src/github.com/tonymackay/cf-purge
RUN git clone https://github.com/tonymackay/cf-purge . && git checkout ${CF_PURGE_VERSION}
RUN go install -ldflags=-X=main.version=${CF_PURGE_VERSION}

# Build Hugo
WORKDIR /go/src/ç
RUN git clone https://github.com/gohugoio/hugo . && git checkout ${HUGO_VERSION}
RUN go get github.com/magefile/mage
RUN mage install

# AWS CLI
WORKDIR /aws 
RUN apt update && apt install unzip -y \
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install
    
# ca-certificates
RUN apt-get install ca-certificates

# Build img-compressor
ENV IMG_COMPRESSOR_VERSION=v1.0.0-beta1
WORKDIR /go/src/github.com/tonymackay/img-compressor
RUN git clone https://github.com/tonymackay/img-compressor . && git checkout ${IMG_COMPRESSOR_VERSION}
RUN go install -ldflags=-X=main.version=${IMG_COMPRESSOR_VERSION}

# NodeJS v12.18.2
FROM node:lts-buster-slim AS node-installer
FROM debian:buster-slim

# copy s3-sync
COPY --from=installer /go/bin/s3-sync /go/bin/s3-sync
# copy cf-purge
COPY --from=installer /go/bin/cf-purge /go/bin/cf-purge
# copy Hugo
COPY --from=installer /go/bin/hugo /go/bin/hugo
# copy aws-cli
COPY --from=installer /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=installer /usr/local/bin/ /usr/local/bin/
# copy ca-certificates
COPY --from=installer /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
# copy img-compressor
COPY --from=installer /go/bin/img-compressor /go/bin/img-compressor
# copy NodeJS
COPY --from=node-installer /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=node-installer /usr/local/bin /usr/local/bin

WORKDIR /site
ENV PATH="/usr/local/bin:${PATH}"
ENV PATH="/go/bin:${PATH}"
ENV PATH="/site/node_modules/.bin:${PATH}"
ENTRYPOINT [""]
