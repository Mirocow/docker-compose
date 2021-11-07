FROM php:7.4.10-fpm-alpine

ARG TIMEZONE=Europe/Moscow
ARG PHP_MEMORY_LIMIT=512M
ARG MAX_UPLOAD=50M
ARG PHP_MAX_FILE_UPLOAD=200
ARG PHP_MAX_POST=100M

# Environments
ENV TIMEZONE=${TIMEZONE}
ENV PHP_MEMORY_LIMIT=${PHP_MEMORY_LIMIT}
ENV MAX_UPLOAD=${MAX_UPLOAD}
ENV PHP_MAX_FILE_UPLOAD=${PHP_MAX_FILE_UPLOAD}
ENV PHP_MAX_POST=${PHP_MAX_POST}

RUN echo "http://dl-cdn.alpinelinux.org/alpine/v3.14/main/" > /etc/apk/repositories && \
	echo "http://dl-cdn.alpinelinux.org/alpine/v3.14/community" >> /etc/apk/repositories 

ENV PHP_INI_DIR /etc/php8

# Let's roll
RUN	 apk update && \
	apk upgrade && \
	apk add --update tzdata && \
	cp "/usr/share/zoneinfo/${TIMEZONE}" /etc/localtime && \
	echo "${TIMEZONE}" > /etc/timezone && \
	apk add --update \
	php8 \
	php8-intl \
	php8-phar \
	php8-fpm \
	php8-dom \
	php8-mcrypt \
	php8-soap \
	php8-openssl \
	php8-gmp \
	php8-pdo_odbc \
	php8-json \
	php8-dom \
	php8-zip \
	php8-bcmath \
	php8-gd \
	php8-odbc \	
	php8-mysqli \
	php8-mysql \
	php8-pgsql \
	php8-pdo \
	php8-sqlite3 \
	php8-pdo_pgsql \
	php8-pdo_mysql \
	php8-pdo_sqlite \
	php8-gettext \
	php8-xmlreader \
	php8-xmlrpc \
	php8-bz2 \
	php8-iconv \
	php8-mbstring \
	php8-pdo_dblib \
	php8-curl \
	php8-ctype \
	php8-redis \
	php8-sockets \
	php8-imagick \
	php8-ssh2 \
	php8-simplexml \
	php8-fileinfo \
	php8-exif \
	php8-pcntl \
	php8-xmlwriter \
	php8-posix \
	php8-gmp \
	php8-tokenizer \
	php8-ftp \
	php8-dev \
	php8-pear \
	php8-pecl-xdebug \
	php8-dev \
	php8-pear \
	&& \
	# Set environments
	sed -i "s|;*daemonize\s*=\s*yes|daemonize = no|g" /etc/php8/php-fpm.conf && \
	sed -i "s|;*listen\s*=\s*127.0.0.1:9000|listen = 9000|g" /etc/php8/php-fpm.d/www.conf && \
	sed -i "s|;*listen\s*=\s*/||g" /etc/php8/php-fpm.d/www.conf && \
	sed -i "s|;*date.timezone =.*|date.timezone = ${TIMEZONE}|i" /etc/php8/php.ini && \
	sed -i "s|;*memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|i" /etc/php8/php.ini && \
	sed -i "s|;*upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|i" /etc/php8/php.ini && \
	sed -i "s|;*max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|i" /etc/php8/php.ini && \
	sed -i "s|;*post_max_size =.*|post_max_size = ${PHP_MAX_POST}|i" /etc/php8/php.ini && \
	sed -i "s|;*cgi.fix_pathinfo=.*|cgi.fix_pathinfo= 0|i" /etc/php8/php.ini && \
	sed -i "s|short_open_tag\s*=\s*Off|short_open_tag = On|i" /etc/php8/php.ini && \
	sed -i "s|display_errors\s*=\s*Off|display_errors = On|i" /etc/php8/php.ini && \	
	sed -i "s|error_reporting\s*=\s*E_ALL\s*&\s*~E_DEPRECATED\s*&\s*~E_STRICT|error_reporting = E_ALL|i" /etc/php8/php.ini && \
	# Cleaning up
	mkdir /app && \
	apk del tzdata && \
	rm -rf /var/cache/apk/*

RUN rm /usr/local/bin/php
RUN rm /usr/local/sbin/php-fpm
RUN rm /usr/local/bin/pecl
RUN ln -s /usr/sbin/php-fpm7 /usr/sbin/php-fpm

RUN apk add --no-cache --allow-untrusted \
	#gnu-libiconv \
	curl \
	wget \
	git \
	libevent \
	yaml \
	geoip \
	geoip-dev \
	bash \
	nano \
	busybox-extras \
	yarn \
	openssh \
	npm \
	bash \
	  && rm -rf /var/cache/apk/*

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer 

# install and remove building packages
ENV PHPIZE_DEPS autoconf file g++ gcc libc-dev make pkgconf \
	yaml-dev pcre-dev zlib-dev cyrus-sasl-dev libevent-dev openssl-dev readline-dev

# Install php extensions
RUN set -xe \
	&& apk add --no-cache \
	--virtual .phpize-deps \
	$PHPIZE_DEPS \
	&& sed -i 's/^exec $PHP -C -n/exec $PHP -C/g' $(which pecl) \
	&& pecl channel-update pecl.php.net \
	&& pecl install -f yaml event \
	&& echo "extension=yaml.so" > $PHP_INI_DIR/conf.d/01_yaml.ini \
	&& echo "extension=event.so" > $PHP_INI_DIR/conf.d/01_event.ini \
	&& rm -rf /usr/share/php8 \
	&& rm -rf /tmp/* \
	&& apk del .phpize-deps && \
	rm -rf /var/cache/apk/*
	
# Set Workdir
WORKDIR /app

# Expose volumes
VOLUME ["/app"]

# Expose ports
#EXPOSE 9000

RUN apk update && apk upgrade && apk add --no-cache --allow-untrusted \
	supervisor \
	&& rm -rf /tmp/* \
	&& rm -rf /var/cache/apk/* \
	&& rm -rvf /var/lib/apt/lists/* \
	rm -vf /etc/ssh/ssh_host_*

RUN mkdir -p /etc/supervisor/conf.d/*.conf

ADD supervisord.conf /etc/supervisor/supervisord.conf
ADD entrypoint.sh /entrypoint.sh

# Entry point
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
