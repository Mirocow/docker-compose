ARG NGINX_VERSION

FROM nginx:$NGINX_VERSION

RUN apk update && apk upgrade && rm -rf /tmp/* \
    && rm -rf /var/cache/apk/* \
    && rm -rvf /var/lib/apt/lists/* \
    rm -vf /etc/ssh/ssh_host_*
