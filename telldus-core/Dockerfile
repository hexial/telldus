FROM debian:12.7 as builder
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
    cmake --build /usr/src/telldus-core/build -- -j$(nproc) \
    && \
    cmake --build /usr/src/telldus-core/build --target install

FROM debian:12.7
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update \
    && \
    apt install -y \
    libftdi1 \
    libconfuse2 \
    && \
    apt clean
COPY --from=builder /etc/tellstick.conf /etc/
COPY --from=builder /usr/local/sbin/telldusd /usr/local/sbin/
COPY --from=builder /var/state/telldus-core.conf /var/state/
COPY --from=builder /usr/local/bin/tdtool /usr/local/bin/
COPY --from=builder /usr/local/lib/libtelldus-core.so.2 /usr/local/lib/
ENTRYPOINT [ "/usr/local/sbin/telldusd", "--nodaemon" ]