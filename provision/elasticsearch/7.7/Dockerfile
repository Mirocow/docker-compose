FROM alpine:3.11
# http://dl-cdn.alpinelinux.org/alpine/v3.11/community/x86_64/

LABEL maintainer "https://github.com/blacktop"

ENV VERSION 7.7.0
ENV DOWNLOAD_URL "https://artifacts.elastic.co/downloads/elasticsearch"
ENV ES_TARBAL "${DOWNLOAD_URL}/elasticsearch-oss-${VERSION}-no-jdk-linux-x86_64.tar.gz"
ENV ES_TARBALL_ASC "${DOWNLOAD_URL}/elasticsearch-oss-${VERSION}-no-jdk-linux-x86_64.tar.gz.asc"
ENV EXPECTED_SHA_URL "${DOWNLOAD_URL}/elasticsearch-oss-${VERSION}-no-jdk-linux-x86_64.tar.gz.sha512"
#ENV ES_TARBALL_SHA "f9b323cff078a0dc856f99efd164775a9cd0718bb0ddb6c4d4817fa53cae71748f1aec5158afa45d4b29076043cbada9743a996e685439436e93d94a9eff2768"
ENV GPG_KEY "46095ACC8548582C1A2699A9D27D666CD88E42B4"
ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk
ENV PATH $PATH:$JAVA_HOME/bin

RUN apk add --no-cache openjdk11-jre-headless openjdk11-jdk su-exec

RUN apk add --no-cache bash
RUN apk add --no-cache -t .build-deps wget ca-certificates gnupg openssl \
  && set -ex \
  && cd /tmp \
  && echo "===> Install Elasticsearch..." \
  && wget --progress=bar:force -O elasticsearch.tar.gz "$ES_TARBAL"; \
  #if [ "$ES_TARBALL_SHA" ]; then \
  #echo "$ES_TARBALL_SHA *elasticsearch.tar.gz" | sha512sum -c -; \
  #fi; \
  if [ "$ES_TARBALL_ASC" ]; then \
  wget --progress=bar:force -O elasticsearch.tar.gz.asc "$ES_TARBALL_ASC"; \
  export GNUPGHOME="$(mktemp -d)"; \
  ( gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
  || gpg --keyserver pgp.mit.edu --recv-keys "$GPG_KEY" \
  || gpg --keyserver keyserver.pgp.com --recv-keys "$GPG_KEY" ); \
  gpg --batch --verify elasticsearch.tar.gz.asc elasticsearch.tar.gz; \
  rm -rf "$GNUPGHOME" elasticsearch.tar.gz.asc || true; \
  fi; \
  tar -xf elasticsearch.tar.gz \
  && ls -lah \
  && mv elasticsearch-$VERSION /usr/share/elasticsearch \
  && adduser -D -h /usr/share/elasticsearch elasticsearch \
  && echo "===> Creating Elasticsearch Paths..." \
  && for path in \
  /usr/share/elasticsearch/data \
  /usr/share/elasticsearch/logs \
  /usr/share/elasticsearch/config \
  /usr/share/elasticsearch/config/scripts \
  /usr/share/elasticsearch/tmp \
  /usr/share/elasticsearch/plugins \
  ; do \
  mkdir -p "$path"; \
  chown -R elasticsearch:elasticsearch "$path"; \
  done \
  && rm -rf /tmp/* /usr/share/elasticsearch/jdk \
  && apk del --purge .build-deps

# TODO: remove this (it removes X-Pack ML so it works on Alpine)
RUN rm -rf /usr/share/elasticsearch/modules/x-pack-ml/platform/linux-x86_64

COPY config/elastic /usr/share/elasticsearch/config
COPY config/logrotate /etc/logrotate.d/elasticsearch
COPY elastic-entrypoint.sh /
RUN chmod +x /elastic-entrypoint.sh
COPY docker-healthcheck /usr/local/bin/

WORKDIR /usr/share/elasticsearch

ENV PATH /usr/share/elasticsearch/bin:$PATH
ENV ES_TMPDIR /usr/share/elasticsearch/tmp

RUN echo $PATH

RUN /usr/share/elasticsearch/bin/elasticsearch-plugin install https://github.com/opendatasoft/elasticsearch-aggregation-geoclustering/releases/download/v7.7.0.0/geopoint-clustering-aggregation-7.7.0.0.zip

VOLUME ["/usr/share/elasticsearch/data"]

EXPOSE 9200 9300
ENTRYPOINT ["/elastic-entrypoint.sh"]
CMD ["elasticsearch"]
