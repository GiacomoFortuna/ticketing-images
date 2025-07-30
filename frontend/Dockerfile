FROM node:20-slim AS build

# Aggiorna i pacchetti e installa git
RUN apt-get update && \
    apt-get install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copia i file sorgenti
COPY ./source/ ./

# Installa le dipendenze (dev incluse, per la build)
RUN npm install --no-audit --no-fund

# Compila e rimuove tutto il superfluo
RUN rm -rf .git && \
    npm run build && \
    rm -rf node_modules src public *.json *.config.* && \
    npm cache clean --force


FROM nginx:alpine

# Update packages
RUN apk upgrade --no-cache


# Crea le directory temporanee e di cache con i permessi corretti per nginx
RUN mkdir -p /tmp/nginx/client_temp /tmp/nginx/proxy_temp /tmp/nginx/fastcgi_temp /tmp/nginx/uwsgi_temp /tmp/nginx/scgi_temp && \
    chown -R nginx:nginx /tmp/nginx && \
    chmod -R 755 /tmp/nginx

# Crea anche le directory di cache standard ma con permessi per nginx
RUN mkdir -p /var/cache/nginx/client_temp /var/cache/nginx/proxy_temp /var/cache/nginx/fastcgi_temp /var/cache/nginx/uwsgi_temp /var/cache/nginx/scgi_temp && \
    chown -R nginx:nginx /var/cache/nginx && \
    chmod -R 755 /var/cache/nginx

# Copy nginx configuration
COPY config/nginx.conf /etc/nginx/nginx.conf

# Copy built files
COPY --from=build /app/dist/ /usr/share/nginx/html/

# Set proper ownership
RUN chown -R nginx:nginx /usr/share/nginx/html /var/log/nginx && \
    chmod -R 755 /usr/share/nginx/html

ARG BUILD_DATE
ARG CI_COMMIT_SHA
ARG CI_COMMIT_REF_NAME
RUN echo "{\"build_date\":\"${BUILD_DATE:-unknown}\",\"commit\":\"${CI_COMMIT_SHA:-unknown}\",\"branch\":\"${CI_COMMIT_REF_NAME:-unknown}\"}" > /usr/share/nginx/html/version.json \
    && chown nginx:nginx /usr/share/nginx/html/version.json


WORKDIR /usr/share/nginx/html

# Switch to nginx user
USER nginx

EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]
