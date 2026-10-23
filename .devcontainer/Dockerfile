# Base image from the Dev Containers project. It already includes PHP,
# Composer, Xdebug and common CLI tools. Change VARIANT in
# docker-compose.yml to pick another PHP version (e.g. 8.4-bookworm).
ARG VARIANT=8.3-bookworm
FROM mcr.microsoft.com/devcontainers/php:1-${VARIANT}

# The base image ships a Yarn apt source whose signing key is no longer
# trusted, which makes `apt-get update` fail. Yarn isn't needed here, so
# remove that source first (-f keeps this harmless if it's fixed upstream).
#
# Then install the MariaDB command-line client, plus build libraries for the
# PHP extensions that Laravel and MariaDB/MySQL access need.
RUN rm -f /etc/apt/sources.list.d/yarn.list \
    && apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        mariadb-client \
        libzip-dev \
        libicu-dev \
    && docker-php-ext-install pdo_mysql mysqli bcmath zip intl \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*
