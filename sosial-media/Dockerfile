FROM alpine:3.11


# make sure you can use HTTPS
RUN apk --update add ca-certificates

RUN echo "https://dl.bintray.com/php-alpine/v3.11/php-7.4" >> /etc/apk/repositories

# Install packages
RUN apk --no-cache add php php-fpm php-opcache php-openssl php-curl php-mysqli php-pdo php-pdo_mysql\
    nginx supervisor curl php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip 
RUN apk --update add \
        php7 \
        php7-bcmath \
        php7-ctype \
        php7-curl \
        php7-fpm \
        php7-gd \
        php7-iconv \
        php7-intl \
        php7-json \
        php7-mbstring \
        php7-mcrypt \
        php7-mysqlnd \
        php7-opcache \
        php7-openssl \
        php7-pdo \
        php7-pdo_mysql \
        php7-pdo_pgsql \
        php7-pdo_sqlite \
        php7-phar \
        php7-posix \
        php7-session \
        php7-soap \
        php7-xml \
        php7-zip

# Configure nginx
COPY config/nginx.conf /etc/nginx/nginx.conf

# Remove default server definition
RUN rm /etc/nginx/conf.d/default.conf

# Configure PHP-FPM
COPY config/fpm-pool.conf /etc/php7/php-fpm.d/www.conf
COPY config/php.ini /etc/php7/conf.d/custom.ini

# Configure supervisord
# Sebuah package manager untuk menjalankan nginx dan php-fpm sekaligus
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Setup document root
RUN mkdir -p /var/www/html

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /var/www/html && \
  chown -R nobody.nobody /run && \
  chown -R nobody.nobody /var/lib/nginx && \
  chown -R nobody.nobody /var/log/nginx

# Switch to use a non-root user from here on
USER nobody

# Add application
WORKDIR /var/www/html
# Mengcopy source code
COPY --chown=nobody src/sosial-media/ /var/www/html/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

