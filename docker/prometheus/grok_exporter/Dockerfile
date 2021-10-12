FROM alpine:3.14.2

# https://wiki.alpinelinux.org/wiki/Running_glibc_programs
# gcompat allows to run glibc binaries in Alpine linux (which uses musl https://musl.libc.org/)
RUN apk update \
    && apk add wget curl unzip bash vim gcompat

# Use separate ENV statements to use previously defined ENV vars in new ones
# If grok_exporter version >= 1.0.0.RC3, use config file version 3
#ENV GROK_EXPORTER_VERSION=0.2.8
ENV GROK_EXPORTER_VERSION=1.0.0.RC5
ENV GROK_EXPORTER_ZIP=grok_exporter-${GROK_EXPORTER_VERSION}.linux-amd64.zip
ENV GROK_EXPORTER_DOWNLOAD_URL=https://github.com/fstab/grok_exporter/releases/download/v${GROK_EXPORTER_VERSION}/$GROK_EXPORTER_ZIP \
    PROMETHEUS_HOME=/opt/prometheus

RUN addgroup --system prometheus \
    && adduser --gecos prometheus --ingroup prometheus --system prometheus \
    && mkdir -p ${PROMETHEUS_HOME} \
    && chown -R prometheus:prometheus ${PROMETHEUS_HOME}


USER prometheus

RUN wget -O ${PROMETHEUS_HOME}/$GROK_EXPORTER_ZIP $GROK_EXPORTER_DOWNLOAD_URL\
    && cd ${PROMETHEUS_HOME} \
    && unzip ${GROK_EXPORTER_ZIP} \
    && mv ${GROK_EXPORTER_ZIP%%.zip} grok_exporter

COPY --chown=prometheus:prometheus ./sample_logs/* "$PROMETHEUS_HOME/sample_logz"

WORKDIR $PROMETHEUS_HOME/grok_exporter
ENTRYPOINT ["./grok_exporter", "-config", "./config.yml"]
