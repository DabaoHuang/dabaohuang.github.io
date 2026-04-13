# Blog Development Environment

This directory contains the Docker Compose setup for local Jekyll development.

## Start

```sh
cd dev
docker compose up --build
```

Open:

```text
http://localhost:4000
```

## Stop

```sh
cd dev
docker compose down
```

The blog source is mounted into the container, so changes to layouts, includes,
posts, and CSS are rebuilt automatically.

`dev/_config.dev.yml` overrides the production URL settings so local navigation
stays on `localhost`.
