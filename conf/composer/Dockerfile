FROM composer:latest

# Install required packages
RUN apk --no-cache add php7-gd freetype-dev libjpeg-turbo-dev libpng-dev

# Install selected extensions
RUN docker-php-ext-configure gd \
    --with-freetype-dir=/usr \
    --with-png-dir=/usr \
    --with-jpeg-dir=/usr \
    && docker-php-ext-install gd