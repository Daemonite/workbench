FROM jwilder/nginx-proxy
MAINTAINER Geoff Bowers <modius@daemon.com.au>

ENV DEFAULT_HOST workbench.dev
RUN { \
      echo 'client_max_body_size 100m;'; \
      echo 'proxy_connect_timeout 10m;'; \
      echo 'proxy_send_timeout 10m;'; \
      echo 'proxy_read_timeout 10m;'; \
      echo 'send_timeout 10m;'; \
    } > /etc/nginx/conf.d/workbench_proxy.conf
