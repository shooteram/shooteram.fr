FROM alpine:3.13.1

RUN apk update && apk add --no-cache php php-fpm php-phar php-json \
    php-mbstring php-iconv php-openssl php-ctype php-xml php-session \
    php-dom supervisor nginx vim

COPY ./.devops/nginx.conf /etc/nginx/conf.d/default.conf
RUN mkdir -p /run/nginx && touch /run/nginx/nginx.pid && \
    nginx -c /etc/nginx/nginx.conf -t

RUN mkdir -p /var/www/shooteram.fr
COPY . /var/www/shooteram.fr
WORKDIR /var/www/shooteram.fr

COPY --from=composer:2.0.9 /usr/bin/composer /usr/bin/composer

RUN chown -R nginx:nginx /var/www/shooteram.fr && \
    chmod ug+rwx -R /var/www/shooteram.fr

USER nginx
RUN composer install --no-ansi --no-dev --no-interaction --no-plugins \
    --no-progress --no-scripts --optimize-autoloader

USER root
RUN ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log && \
    rm -f /etc/nginx/sites-enabled/* && \
    sed -i -e "s/user = nobody/user = nginx/g" /etc/php7/php-fpm.d/www.conf && \
    sed -i -e "s/user = nobody/user = nginx/g" /etc/php7/php-fpm.d/www.conf

COPY ./.devops/supervisord.conf /etc/supervisord.conf
ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"]
EXPOSE 80 443
