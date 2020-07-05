FROM golang:buster AS installer

ENV S3_SYNC_VERSION=v1.0.0-beta2
ENV CF_PURGE_VERSION=v1.0.0-beta1
ENV HUGO_VERSION=v0.73.0
ENV IMG_COMPRESSOR_VERSION=v1.0.0-beta1
ENV GUETZLI_VERSION=1.0.1
ENV ZOPFLIPNG_VERSION=1.0.3

# Build guetzli
WORKDIR /guetzli
RUN apt-get update && apt-get install libpng-dev -y
RUN git clone https://github.com/google/guetzli.git . && git checkout v${GUETZLI_VERSION}
RUN make config=release
RUN ldd /guetzli/bin/Release/guetzli

# Build zopflipng
WORKDIR /zopflipng
RUN git clone https://github.com/google/zopfli.git . && git checkout zopfli-${ZOPFLIPNG_VERSION}
RUN make zopflipng
RUN ldd /zopflipng/zopflipng

# Build s3-sync
WORKDIR /go/src/github.com/tonymackay/s3-sync
RUN git clone https://github.com/tonymackay/s3-sync . && git checkout ${S3_SYNC_VERSION}
RUN go install -ldflags=-X=main.version=${S3_SYNC_VERSION}

# Build cf-purge
WORKDIR /go/src/github.com/tonymackay/cf-purge
RUN git clone https://github.com/tonymackay/cf-purge . && git checkout ${CF_PURGE_VERSION}
RUN go install -ldflags=-X=main.version=${CF_PURGE_VERSION}

# Build Hugo
WORKDIR /go/src/github.com/gohugoio/hugo
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
WORKDIR /go/src/github.com/tonymackay/img-compressor
RUN git clone https://github.com/tonymackay/img-compressor . && git checkout ${IMG_COMPRESSOR_VERSION}
RUN go install -ldflags=-X=main.version=${IMG_COMPRESSOR_VERSION}

# NodeJS v12.18.2
FROM node:lts-buster-slim AS node-installer
FROM debian:buster-slim

# copy guetzli and zopflipng
COPY --from=installer /guetzli/bin/Release/guetzli /usr/local/bin/guetzli
COPY --from=installer /zopflipng/zopflipng /usr/local/bin/zopflipng

# copy guetzli and zopflipng dependencies
COPY --from=installer /usr/lib/x86_64-linux-gnu/libpng16.so.16 /usr/lib/x86_64-linux-gnu/libpng16.so.16
COPY --from=installer /lib/x86_64-linux-gnu/libz.so.1 /lib/x86_64-linux-gnu/libz.so.1
COPY --from=installer /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /usr/lib/x86_64-linux-gnu/libstdc++.so.6
COPY --from=installer /lib/x86_64-linux-gnu/libm.so.6 /lib/x86_64-linux-gnu/libm.so.6
COPY --from=installer /lib/x86_64-linux-gnu/libgcc_s.so.1 /lib/x86_64-linux-gnu/libgcc_s.so.1
COPY --from=installer /lib/x86_64-linux-gnu/libc.so.6 /lib/x86_64-linux-gnu/libc.so.6
COPY --from=installer /lib64/ld-linux-x86-64.so.2 /lib64/ld-linux-x86-64.so.2

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
