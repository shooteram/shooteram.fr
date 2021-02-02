FROM alpine

RUN apk update && apk add php && \
    mkdir -p /var/www/shooteram.fr && \
    echo "<?php phpinfo();" > /var/www/shooteram.fr/index.php

ENTRYPOINT ["php", "-S", "0.0.0.0:80", "-t", "/var/www/shooteram.fr"]
EXPOSE 80
