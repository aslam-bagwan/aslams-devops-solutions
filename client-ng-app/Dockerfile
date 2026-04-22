FROM node:20-alpine AS build
WORKDIR /src

COPY . .

RUN if [ -f package-lock.json ]; then npm ci; else npm install; fi

RUN npm run build

RUN output_dir="$(find dist -mindepth 1 -maxdepth 2 -type d | sort | head -n 1)" \
    && test -n "$output_dir" \
    && mkdir -p /tmp/web \
    && cp -R "$output_dir"/. /tmp/web/

FROM nginxinc/nginx-unprivileged:1.27-alpine AS final

COPY --from=build /tmp/web /usr/share/nginx/html

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 CMD wget -qO- http://127.0.0.1:8080/ > /dev/null || exit 1