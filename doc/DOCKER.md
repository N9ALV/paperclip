# Docker Quickstart

Run Paperclip in Docker without installing Node or pnpm locally.

## One-liner (build + run)

```sh
docker build -t paperclip-local . && \
docker run --name paperclip \
  -p 3100:3100 \
  -e HOST=0.0.0.0 \
  -e PAPERCLIP_HOME=/paperclip \
  -v "$(pwd)/data/docker-paperclip:/paperclip" \
  paperclip-local
```

Open: `http://localhost:3100`

Data persistence:

- Embedded PostgreSQL data
- uploaded assets
- local secrets key
- local agent workspace data

All persisted under your bind mount (`./data/docker-paperclip` in the example above).

## Compose Quickstart

```sh
docker compose -f docker-compose.quickstart.yml up --build
```

Defaults:

- host port: `3100`
- persistent data dir: `./data/docker-paperclip`

Optional overrides:

```sh
PAPERCLIP_PORT=3200 PAPERCLIP_DATA_DIR=./data/pc docker compose -f docker-compose.quickstart.yml up --build
```

## Railway Deployment Notes

Railway blocks the Dockerfile `VOLUME` instruction. Paperclip's Dockerfile intentionally does not declare `VOLUME`; configure persistence in Railway itself.

Recommended Railway setup:

- Deploy from GitHub repo (remote build on Railway)
- Add a Railway Volume mounted at `/paperclip`
- Set env vars:
  - `HOST=0.0.0.0`
  - `PORT=${{PORT}}` (or leave default and let Railway inject `PORT`)
  - `PAPERCLIP_HOME=/paperclip`
  - `PAPERCLIP_CONFIG=/paperclip/instances/default/config.json`
  - `PAPERCLIP_DEPLOYMENT_MODE=authenticated` for internet-exposed deployments
  - `OPENAI_API_KEY` and/or `ANTHROPIC_API_KEY` as needed for adapters

This keeps embedded Postgres, local secrets key, and storage persisted on the Railway Volume.

## Claude + Codex Local Adapters in Docker

The image pre-installs:

- `claude` (Anthropic Claude Code CLI)
- `codex` (OpenAI Codex CLI)

If you want local adapter runs inside the container, pass API keys when starting the container:

```sh
docker run --name paperclip \
  -p 3100:3100 \
  -e HOST=0.0.0.0 \
  -e PAPERCLIP_HOME=/paperclip \
  -e OPENAI_API_KEY=... \
  -e ANTHROPIC_API_KEY=... \
  -v "$(pwd)/data/docker-paperclip:/paperclip" \
  paperclip-local
```

Notes:

- Without API keys, the app still runs normally.
- Adapter environment checks in Paperclip will surface missing auth/CLI prerequisites.
