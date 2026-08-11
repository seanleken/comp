# Build with repo root as context via apps/api/cloudbuild.yaml (gcloud builds submit
# --tag has no -f/--dockerfile flag, so a Dockerfile outside the source root needs a
# cloudbuild.yaml build step instead):
#   gcloud builds submit --config=apps/api/cloudbuild.yaml --substitutions=_IMAGE=<image> .

FROM oven/bun:1.2.8 AS builder

WORKDIR /app

COPY . .

RUN PRISMA_SKIP_POSTINSTALL_GENERATE=true bun install --ignore-scripts
RUN if [ -f node_modules/bun/install.js ]; then node node_modules/bun/install.js; fi

ENV NODE_OPTIONS=--max-old-space-size=3072

RUN cd apps/api && bun run build:docker

FROM node:22-alpine AS api

WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/apps/api/dist ./apps/api/dist
COPY --from=builder /app/apps/api/package.json ./apps/api/package.json

EXPOSE 3333
CMD ["node", "apps/api/dist/main"]
