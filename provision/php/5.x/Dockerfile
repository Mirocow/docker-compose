FROM php:5.6-fpm-alpine

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

RUN echo "http://dl-cdn.alpinelinux.org/alpine/v3.5/main/" > /etc/apk/repositories && \
	echo "http://dl-cdn.alpinelinux.org/alpine/v3.5/community" >> /etc/apk/repositories

# Let's roll
RUN	 apk update && \
	apk upgrade && \
	apk add --update tzdata && \
	cp "/usr/share/zoneinfo/${TIMEZONE}" /etc/localtime && \
	echo "${TIMEZONE}" > /etc/timezone && \
	apk add --no-cache --update \
	nano \
	#busybox-extras \
	bash \
	#tini \
	php5 \
	php5-intl \
	php5-phar \
	php5-fpm \
	php5-dom \
	php5-mcrypt \
	php5-soap \
	php5-openssl \
	php5-gmp \
	php5-pdo_odbc \
	php5-json \
	php5-dom \
	php5-pdo \
	php5-zip \
	php5-mysqli \
	php5-sqlite3 \
	php5-bcmath \
	php5-gd \
	php5-odbc \
	php5-pdo_mysql \
	php5-pdo_sqlite \
	php5-gettext \
	php5-xmlreader \
	php5-xmlrpc \
	php5-bz2 \
	php5-iconv \
	#php5-mbstring \
	php5-pdo_dblib \
	php5-curl \
	php5-ctype \
	php5-sockets \
	php5-exif \
	php5-pcntl \
	php5-posix \
	php5-gmp \
	#php5-session \
	php5-xdebug \
	php5-zlib \
	php5-dev \
	php5-pear \
	&& \
	  # Set environments
	sed -i "s|;*daemonize\s*=\s*yes|daemonize = no|g" /etc/php5/php-fpm.conf && \
	sed -i "s|;*listen\s*=\s*127.0.0.1:9000|listen = 9000|g" /etc/php5/php-fpm.conf && \
	sed -i "s|;*listen\s*=\s*/||g" /etc/php5/php-fpm.conf && \
	sed -i "s|;*date.timezone =.*|date.timezone = ${TIMEZONE}|i" /etc/php5/php.ini && \
	sed -i "s|;*memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|i" /etc/php5/php.ini && \
	sed -i "s|;*upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|i" /etc/php5/php.ini && \
	sed -i "s|;*max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|i" /etc/php5/php.ini && \
	sed -i "s|;*post_max_size =.*|post_max_size = ${PHP_MAX_POST}|i" /etc/php5/php.ini && \
	sed -i "s|;*cgi.fix_pathinfo=.*|cgi.fix_pathinfo= 0|i" /etc/php5/php.ini && \
	sed -i "s|short_open_tag\s*=\s*Off|short_open_tag = On|i" /etc/php5/php.ini && \
	sed -i "s|display_errors\s*=\s*Off|display_errors = On|i" /etc/php5/php.ini && \	
	sed -i "s|error_reporting\s*=\s*E_ALL\s*&\s*~E_DEPRECATED\s*&\s*~E_STRICT|error_reporting = E_ALL|i" /etc/php5/php.ini && \
	# Cleaning up
	mkdir /app && \
	apk del tzdata && \
	rm -rf /var/cache/apk/*

RUN php -v
RUN rm /usr/local/bin/php
RUN rm /usr/local/bin/pecl
#RUN mv /usr/bin/php /usr/bin/php.old		
#RUN ln -s /usr/bin/php5 /usr/bin/php
#RUN ln -s /usr/sbin/php-fpm7 /usr/sbin/php-fpm

RUN apk add --no-cache --allow-untrusted \
	curl \
	wget \
	git \
	libevent \
	libssl1.0 \
	yaml \
	geoip \
	geoip-dev \
	bash \
	nano \
	#yarn \
	openssh \
	bash \
	  && rm -rf /var/cache/apk/*

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

# install and remove building packages
ENV PHPIZE_DEPS autoconf file g++ gcc libc-dev make pkgconf \
	yaml-dev pcre-dev zlib-dev cyrus-sasl-dev libevent-dev openssl-dev readline-dev

ENV PHP_INI_DIR /etc/php5

RUN set -xe \
	&& apk add --no-cache \
	--virtual .phpize-deps \
	$PHPIZE_DEPS \
	&& sed -i 's/^exec $PHP -C -n/exec $PHP -C/g' $(which pecl) \
	&& pecl channel-update pecl.php.net \
	&& pecl install yaml event \
	#&& echo "extension=yaml.so" > $PHP_INI_DIR/conf.d/01_yaml.ini \
	&& echo "extension=event.so" > $PHP_INI_DIR/conf.d/01_event.ini \
	&& rm -rf /usr/share/php5 \
	&& rm -rf /tmp/* \
	&& apk del .phpize-deps && \
	rm -rf /var/cache/apk/*

RUN curl -L https://github.com/sequenceiq/docker-alpine-dig/releases/download/v9.10.2/dig.tgz|tar -xzv -C /usr/local/bin/

# Set Workdir
WORKDIR /app

# Expose volumes
VOLUME ["/app"]

# Expose ports
#EXPOSE 9000

# Entry point
CMD ["php-fpm5", "-F"]
