___
created: 20250422
___

# service-hoarder

## content

### config

on Node NETER docker-compose:

```yml
services:
  web:
    image: ghcr.io/karakeep-app/karakeep:${KARAKEEP_VERSION:-release}
    restart: unless-stopped
    volumes:
      # By default, the data is stored in a docker volume called "data".
      # If you want to mount a custom directory, change the volume mapping to:
      # - /path/to/your/directory:/data
      - ${VOLUME}/hoarder/data:/data
    ports:
      - ${PORT}:3000
    #env_file:
    #  - .env
    environment:
      MEILI_ADDR: http://meilisearch:7700
      BROWSER_WEB_URL: http://chrome:9222
      NEXTAUTH_SECRET: ${NEXTAUTH_SECRET}
      OPENAI_API_KEY: ${OPENAI_API_KEY}

      # You almost never want to change the value of the DATA_DIR variable.
      # If you want to mount a custom directory, change the volume mapping above instead.
      DATA_DIR: /data # DON'T CHANGE THIS
  chrome:
    image: gcr.io/zenika-hub/alpine-chrome:123
    restart: unless-stopped
    command:
      - --no-sandbox
      - --disable-gpu
      - --disable-dev-shm-usage
      - --remote-debugging-address=0.0.0.0
      - --remote-debugging-port=9222
      - --hide-scrollbars
  meilisearch:
    image: getmeili/meilisearch:v1.13.3
    restart: unless-stopped
    #env_file:
    #  - .env
    environment:
      MEILI_NO_ANALYTICS: "true"
    volumes:
      - ${VOLUME}/hoarder/meilisearch:/meili_data

volumes:
  meilisearch:
  data:
```
.env:

```{.env}
OPENAI_API_KEY=
PORT=#not3000
NEXTAUTH_URL=
NEXTAUTH_SECRET=
MEILI_MASTER_KEY=
VOLUME=/srv/docker
DATA_DIR=/data
MEILI_ADDR=http://127.0.0.1:7700
```

## refs

### up

[[homelab]]

### down