FROM node:13.10.1-alpine3.11

# Caddy
RUN apk add --no-cache curl unzip libcap \
 && curl --silent --show-error --fail --location --header "Accept: application/tar+gzip, application/x-gzip, application/octet-stream" -o - \
    "https://caddyserver.com/download/linux/amd64?license=personal&telemetry=off" | tar --no-same-owner -C /usr/bin/ -xz caddy \
 && chmod 0755 /usr/bin/caddy \
 && addgroup -S caddy \
 && adduser -D -S -s /sbin/nologin -G caddy caddy \
 && setcap cap_net_bind_service=+ep `readlink -f /usr/bin/caddy` \
 && /usr/bin/caddy -version

WORKDIR /app

# Localtunnel
COPY package.json yarn.lock /app/
RUN yarn install --production && yarn cache clean
COPY . /app
RUN chown -R caddy:caddy /app

COPY docker/Caddyfile /etc/Caddyfile
VOLUME /etc/ssl

USER caddy
ENV NODE_ENV production
ENTRYPOINT ["/usr/bin/caddy", "--conf", "/etc/Caddyfile"]
