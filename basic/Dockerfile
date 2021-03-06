# Build container for GitPitch
FROM bigtruedata/sbt:0.13.15-alpine-2.11.11 as SBTBuild
RUN apk add --update --no-cache git unzip && \
    git clone https://github.com/gitpitch/gitpitch.git /gitpitch-git && \
    cd /gitpitch-git && \
    sbt dist && \
    unzip /gitpitch-git/target/universal/server-*.zip -d /tmp && \
    mv /tmp/server-* /gitpitch

# Runtime container
FROM java:8-jre-alpine

ARG GOMPLATE_VERSION="v2.3.0"

ENV GITPITCH_VERSION=2.0

# metadata
LABEL org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.name="GitPitch" \
      org.label-schema.description="GitPitch in basic version without PDF export support" \
      org.label-schema.url="https://github.com/kns-it/Docker-GitPitch" \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.vcs-url="https://github.com/kns-it/Docker-GitPitch" \
      org.label-schema.vendor="KNS" \
      org.label-schema.version=$GITPITCH_VERSION \
      org.label-schema.schema-version="1.0" \
      maintainer="sebastian.kurfer@kns-it.de"

ADD https://github.com/hairyhenderson/gomplate/releases/download/$GOMPLATE_VERSION/gomplate_linux-amd64-slim /usr/bin/gomplate

# copy from build containers
COPY --from=SBTBuild /gitpitch /gitpitch

# setup runtime environment
RUN apk add --update --no-cache zip \
                                bash \
                                fontconfig && \
    chmod +x /usr/bin/gomplate && \
    adduser -h /home/gitpitch -s /bin/sh -D gitpitch && \
    mkdir /etc/gitpitch && \
    chown -R gitpitch /etc/gitpitch && \
    chown -R gitpitch /gitpitch

ADD ./bin/run-gitpitch.sh /gitpitch/bin/run-gitpitch.sh
ADD ./conf/application.conf.template /gitpitch/conf/application.conf.template

EXPOSE 9000

USER gitpitch

CMD ["/gitpitch/bin/run-gitpitch.sh"]
