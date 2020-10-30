FROM alpine as builder

RUN set -xe \
    && apk add --no-cache \
        autoconf \
        automake \
        build-base \
        cmake \
        git \
        libtool \
        zlib-dev \
        linux-headers

ENV GRPC_VERSION v1.33.1

RUN if [[ -z "$GRPC_VERSION" ]]; then echo "GRPC_VERSION argument MUST be set" && exit 1; fi

RUN git clone --depth 1 --recursive -b ${GRPC_VERSION} https://github.com/grpc/grpc.git /grpc

ENV LDFLAGS=-static

RUN mkdir -p /grpc/cmake/build \
  && cd /grpc/cmake/build \
  && cmake -DgRPC_BUILD_TESTS=ON ../.. \
  && make grpc_cli

FROM scratch
COPY --from=builder /grpc/cmake/build/grpc_cli /grpc_cli
COPY --from=builder /grpc/etc/roots.pem /usr/local/share/grpc/roots.pem

ENTRYPOINT ["/grpc_cli"]
CMD ["help"]
