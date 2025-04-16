ARG BUILD_FROM=ghcr.io/hassio-addons/base:17.2.4
FROM ${BUILD_FROM}

    # Environment
ARG BUILD_ARCH
ENV PROMETHEUS_VERSION=3.2.1

# Copy root filesystem
COPY rootfs /

RUN \
    ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    && apk --no-cache add \
        python3=3.12.10-r0 \
        py3-idna=3.10-r0 \
        py3-certifi=2024.8.30-r0 \
        py3-chardet=5.2.0-r1 \
        py3-yaml=6.0.2-r0 \
        py3-urllib3=1.26.20-r0 \
        py3-requests=2.32.3-r0 \
    && apk --no-cache add --virtual .builddeps \
        py-pip=24.3.1-r0 \
    && curl -J -L -o /tmp/prometheus.tar.gz \
        "https://github.com/prometheus/prometheus/releases/download/v${PROMETHEUS_VERSION}/prometheus-${PROMETHEUS_VERSION}.linux-${BUILD_ARCH}.tar.gz" \
    && cd /tmp \
    && tar xvf \
        /tmp/prometheus.tar.gz \
    && adduser -s /bin/false -D -H prometheus \
    && mkdir -p /etc/prometheus \
    && cp /tmp/prometheus-${PROMETHEUS_VERSION}.linux-${ARCH}/promtool /usr/local/bin/ \
    && cp /tmp/prometheus-${PROMETHEUS_VERSION}.linux-${ARCH}/prometheus /usr/local/bin/ \
    && chown -R prometheus:prometheus /etc/prometheus \
    && pip3 install \
        --no-cache-dir \
        --prefer-binary \
        -r /opt/prometheus-configgen/requirements.txt \
    \
    && apk --no-cache del .builddeps \
    && rm -f -r \
        /tmp/*

    # Build arguments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION