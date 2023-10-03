FROM debian:bookworm-slim

ARG BUILD_NUMBER=0
ARG REPOSITORY=sogo
ARG VCS_REF
ARG BUILD_DATE

LABEL maintainer="Mithrandir1 <https://github.com/mithrandir1>" \
      version="5.9.0-${BUILD_NUMBER}" \
      description="SOGo is a groupware server with a focus on scalability and open standards." \
      name=${REPOSITORY} \
      release=${BUILD_NUMBER} \
      build-date=${BUILD_DATE} \
      vcs-ref=${VCS_REF}

ENV TZ=Europe/Vienna

ARG DEBIAN_FRONTEND=noninteractive
ARG SOGO_DEBIAN_REPOSITORY=http://packages.sogo.nu/nightly/5/debian/
ENV LC_ALL C

# Prerequisites
RUN echo "Building from repository $SOGO_DEBIAN_REPOSITORY" \
  && apt-get update && apt-get install -y --no-install-recommends \
  apt-transport-https \
  ca-certificates \
  gnupg \
  && export GNUPGHOME=$(mktemp -d) \
  && gpg --keyserver keys.openpgp.org --recv-key 74FFC6D72B925A34B5D356BDF8A27B36A6E2EAE9 \
  && gpg --export --armor --output /etc/apt/trusted.gpg.d/sogo.gpg.asc 74FFC6D72B925A34B5D356BDF8A27B36A6E2EAE9 \
  && rm -rf ${GNUPGHOME} && unset GNUPGHOME \
  && echo "deb ${SOGO_DEBIAN_REPOSITORY} bookworm bookworm" > /etc/apt/sources.list.d/sogo.list \
  && apt-get update && apt-get install -y --no-install-recommends \
    sogo \
    sogo-activesync \
    ssmtp \
    cron \
  && apt-get autoclean \
  && rm -rf /var/lib/apt/lists/* /etc/apt/sources.list.d/sogo.list \
  && touch /etc/default/locale

COPY sogo.conf /etc/sogo/
USER sogo
CMD [ "/usr/sbin/sogod", "-WOWorkersCount", "3", "-WOLogFile", "/dev/stdout", "-WONoDetach", "YES" ]