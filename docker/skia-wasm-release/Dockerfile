# Dockerfile for building the WASM libraries used by jsfiddle.skia.org and debugger.skia.org
FROM gcr.io/skia-public/emsdk-base:prod as builder

# Checkout Skia.
RUN mkdir -p /tmp/skia \
  && cd /tmp/skia \
  && fetch skia \
  && cd skia \
  && gclient sync \
  && ./bin/fetch-gn

# PathKit should be in /tmp/skia/skia/out/pathkit/
RUN /tmp/skia/skia/modules/pathkit/compile.sh

# CanvasKit should be in /tmp/skia/skia/out/canvaskit_wasm
RUN /tmp/skia/skia/modules/canvaskit/compile.sh

# Debugger should be in /tmp/skia/skia/out/debugger_wasm
RUN /tmp/skia/skia/experimental/wasm-skp-debugger/compile.sh

RUN cd /tmp/skia/skia && git rev-parse HEAD > /tmp/VERSION

#############################################################################
# Multi-stage build part 2, in which we only have the compiled results and
# a VERSION in /tmp
# See https://docs.docker.com/develop/develop-images/multistage-build/
#############################################################################

FROM alpine:latest

WORKDIR /tmp/

RUN mkdir /tmp/pathkit /tmp/canvaskit

COPY --from=builder /tmp/VERSION /tmp/VERSION

COPY --from=builder /tmp/skia/skia/out/pathkit/pathkit* /tmp/pathkit/

COPY --from=builder /tmp/skia/skia/out/canvaskit_wasm/canvaskit* /tmp/canvaskit/

COPY --from=builder /tmp/skia/skia/out/debugger_wasm/debugger* /tmp/debugger/
