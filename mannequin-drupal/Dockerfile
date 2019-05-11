FROM composer:1.8.5
WORKDIR /opt/mannequin
RUN composer require 'lastcall/mannequin-drupal:1.0.7'
WORKDIR /app
VOLUME /app
EXPOSE 80
ENTRYPOINT ["/opt/mannequin/vendor/bin/mannequin" "start" "*:80"]