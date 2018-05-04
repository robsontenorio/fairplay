FROM zuohuadong/caddy:alpine

LABEL maintainer="Robson Tenório <robsontenorio@me.com>"

ARG plugins="cors"

## ARG plugins="cors cgi cloudflare azure linode"

RUN caddyplug install ${plugins}

EXPOSE 80 443 2015

WORKDIR /var/www

CMD ["/usr/bin/caddy", "-conf", "/etc/Caddyfile", "--agree=true"]