FROM blacktop/elasticsearch:6.8

RUN apk update && apk upgrade && rm -rf /tmp/* \
    && rm -rf /var/cache/apk/* \
    && rm -rvf /var/lib/apt/lists/* \
    rm -vf /etc/ssh/ssh_host_*

# https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docker.html
