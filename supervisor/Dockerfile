FROM php

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    supervisor cron

COPY supervisord.conf /etc/supervisor/supervisord.conf

RUN touch /var/log/cron.log

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
