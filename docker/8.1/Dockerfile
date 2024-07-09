FROM webdevops/php-nginx:8.1

LABEL maintainer="Code Sense"

ARG WWWGROUP
ARG NODE_VERSION=21
ARG REDIS_LIB_VERSION=5.3.7

WORKDIR /app

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=America/Sao_Paulo

ENV PHP_UPLOAD_MAX_FILESIZE=100M
ENV PHP_POST_MAX_SIZE=100M
ENV PHP_DATE_TIMEZONE=UTC
ENV php.variables_order=EGPCS

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN   curl -fsSL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get update \
    && apt-get install -y nodejs build-essential net-tools \
    && npm install -g npm

RUN groupadd --force -g $WWWGROUP sail
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail

RUN pecl uninstall -r redis \
    && pecl install redis-${REDIS_LIB_VERSION} \
    && docker-php-ext-enable redis

RUN mkdir -p /var/lib/redis && chown -R sail:sail /var/lib/redis

RUN pecl uninstall -r mongodb \
    && pecl install mongodb \
    && docker-php-ext-enable mongodb

COPY vhost.conf /opt/docker/etc/nginx
COPY 10-location-root.conf /opt/docker/etc/nginx/vhost.common.d
COPY supervisord.conf /opt/docker/etc/supervisor.d/marketplace.conf

EXPOSE 80 443 6001
