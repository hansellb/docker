# FROM openjdk:11-jre-slim # Good for SonarQube > 7
FROM openjdk:8-jre-slim

RUN apt-get update \
    && apt-get install -y curl gnupg2 unzip \
    && rm -rf /var/lib/apt/lists/*

# ENV SONARQUBE_VERSION=7.9.3 \
ENV SONARQUBE_VERSION=5.2 \
    SONARQUBE_HOME=/opt/sonarqube \
    SONARQUBE_JDBC_USERNAME=sonar \
    SONARQUBE_JDBC_PASSWORD=sonar \
    SONARQUBE_JDBC_URL=""

# Http port
EXPOSE 9000

RUN groupadd -r sonarqube && useradd -r -g sonarqube sonarqube

# pub   2048R/D26468DE 2015-05-25
#       Key fingerprint = F118 2E81 C792 9289 21DB  CAB4 CFCA 4A29 D264 68DE
# uid                  sonarsource_deployer (Sonarsource Deployer) <infra@sonarsource.com>
# sub   2048R/06855C1D 2015-05-25
#RUN for server in $(shuf -e ha.pool.sks-keyservers.net \
#                            hkp://p80.pool.sks-keyservers.net:80 \
#                            keyserver.ubuntu.com \
#                            hkp://keyserver.ubuntu.com:80 \
#                            pgp.mit.edu) ; do \
#        gpg --batch --keyserver "$server" --recv-keys F1182E81C792928921DBCAB4CFCA4A29D26468DE && break || : ; \
#    done

COPY sonarqube-${SONARQUBE_VERSION}.zip /opt/sonarqube.zip
COPY --chown=sonarqube:sonarqube ./plugins /tmp/plugins

SHELL ["/bin/bash", "-c"]

RUN set -x \
    && cd /opt \
#    && curl -o sonarqube.zip -fSL https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-$SONARQUBE_VERSION.zip \
#    && curl -o sonarqube.zip.asc -fSL https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-$SONARQUBE_VERSION.zip.asc \
#    && gpg --batch --verify sonarqube.zip.asc sonarqube.zip \
    && unzip -q sonarqube.zip \
    && mv sonarqube-$SONARQUBE_VERSION sonarqube \
    && chown -R sonarqube:sonarqube sonarqube \
    && rm sonarqube.zip* \
    && rm -rf $SONARQUBE_HOME/bin/* \
    && cp $SONARQUBE_HOME/lib/bundled-plugins/* $SONARQUBE_HOME/extensions/plugins \
    && cp /tmp/plugins/*.jar $SONARQUBE_HOME/extensions/plugins \
    && rm -rf /tmp/plugins

VOLUME "$SONARQUBE_HOME/data"

COPY --chown=sonarqube:sonarqube run.sh "$SONARQUBE_HOME/bin/run.sh"

USER sonarqube
WORKDIR $SONARQUBE_HOME
ENTRYPOINT ["./bin/run.sh"]
