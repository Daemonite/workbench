FROM jwilder/nginx-proxy
ENV DEFAULT_HOST workbench.dev
RUN { \
      echo 'client_max_body_size 100m;'; \
    } > /etc/nginx/conf.d/workbench_proxy.conf