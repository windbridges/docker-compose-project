FROM php:8.2-cli AS base

# Install cron
RUN apt-get -y install cron
COPY cron/cron /etc/cron.d/cron
RUN chmod 0644 /etc/cron.d/cron
RUN crontab /etc/cron.d/cron
