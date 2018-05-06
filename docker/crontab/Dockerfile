ARG PHP_VERSION=${PHP_VERSION}
FROM php:${PHP_VERSION}-cli-alpine

RUN docker-php-ext-install mysqli mbstring pdo pdo_mysql

COPY mycrontab /mycrontab

RUN mkdir /var/log/cron
RUN touch /var/log/cron/cron.log

RUN crontab /mycrontab

CMD ["crond", "-f", "-l", "8"]
