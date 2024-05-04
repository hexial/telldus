FROM debian:12.5 as builder
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update \
    && \
    apt install -y \
    build-essential \
    cmake \
    libftdi-dev \
    libconfuse-dev \
    && \
    apt clean
COPY . /usr/src/telldus-core
RUN cmake \
    -S /usr/src/telldus-core \
    -B /usr/src/telldus-core/build \
    -DFORCE_COMPILE_FROM_TRUNK=TRUE \
    -DBUILD_TDTOOL=TRUE \
    -DBUILD_TDADMIN=FALSE \
    -DGENERATE_MAN=FALSE \
    && \
    cmake --build /usr/src/telldus-core/build -- -j$(nproc) 

FROM debian:12.5
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update \
    && \
    apt install -y \
    libftdi1 \
    libconfuse2 \
    && \
    apt clean
COPY --from=builder /usr/src/telldus-core/build/service/telldusd /usr/bin/
COPY --from=builder /usr/src/telldus-core/build/tdtool/tdtool /usr/bin/
ENTRYPOINT [ "/usr/bin/telldusd", "--nodaemon" ]